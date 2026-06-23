# Claude Code Iteration Prompts
## Targeted prompts for Python coding, debugging, and dashboard work

These are optimized for use inside a `claude` terminal session from the
`iowa-dc-impact/` project directory. They are specific, self-contained,
and assume Claude Code has read access to all project files.

Start a session: `cd iowa-dc-impact && claude`

---

### PROMPT: debug-api-collector
```
USE WHEN: A collector script is returning empty data or erroring
PASTE INTO: Claude Code
```

The [COLLECTOR NAME — e.g., eia_collector.py] is not returning data as expected.

Read src/collectors/[FILE].py and then run it with these parameters:
[PASTE THE EXACT CALL THAT FAILED]

The error or unexpected behavior is:
[PASTE THE ERROR MESSAGE OR DESCRIBE THE EMPTY/WRONG RESULT]

Debug by:
1. Printing the exact URL being called and all query parameters
2. Making the HTTP request with verbose output (print response status, headers, first 500 chars of body)
3. Checking if the API returns an error payload vs. empty results vs. wrong data
4. Testing with a smaller/simpler query first (e.g., single year, single county)
5. Checking the API documentation URL in the file's docstring for parameter changes

Fix the issue and show me the corrected code. Then re-run and confirm it returns data.
```

---

### PROMPT: add-dashboard-tab
```
USE WHEN: Adding a new analysis tab to the Panel SPA notebook
PASTE INTO: Claude Code
```

Read notebooks/04_dashboard_spa.ipynb and understand the existing tab structure.

Add a new tab called "[TAB NAME]" that shows:
[DESCRIBE WHAT THE TAB SHOULD DISPLAY]

Requirements:
- Follow the existing pattern: use pn.depends() decorators for reactive widgets
- Add widgets to control: [LIST WIDGETS — e.g., "county selector, year range slider"]
- Create Plotly figures (not matplotlib — must be interactive)
- The tab should work with the current synthetic data AND with real data once loaded
- Add the tab to the pn.Tabs() assembly at the bottom of the notebook in the right position

Data available in the notebook namespace:
- facilities (DataFrame) — facility-level data
- county_agg (DataFrame) — county aggregates
- ts (DataFrame) — time series 2015-2025
- scenarios_df (DataFrame) — scenario projections
- gw_df (DataFrame) — groundwater trends
- grid_df (DataFrame) — Iowa grid fuel mix

Write the complete cell code and tell me exactly where to insert it in the notebook
(after which existing cell).
```

---

### PROMPT: fix-chart
```
USE WHEN: A specific chart in the dashboard looks wrong or needs improvement
PASTE INTO: Claude Code
```

Read notebooks/04_dashboard_spa.ipynb.

The chart in [TAB NAME] tab has this problem:
[DESCRIBE THE ISSUE — e.g., "the y-axis is inverted for groundwater depth, making
declining aquifer levels look like improvement instead of stress"]

Fix it by:
1. Finding the exact cell containing this chart
2. Making the minimal change needed to fix the problem
3. Checking that the fix doesn't break reactive bindings
4. Adding a clear axis label or annotation that explains what "up" and "down" mean

Also check: are there any other charts in the notebook with the same issue?
```

---

### PROMPT: add-real-data-swap
```
USE WHEN: A collector has returned real data and you want to wire it into the dashboard
PASTE INTO: Claude Code
```

I have collected real [DATA TYPE — e.g., "Iowa groundwater levels from USGS NWIS"]
and saved it to data/processed/[FILENAME].parquet.

Read that file and compare its schema to the synthetic [VARIABLE NAME — e.g., gw_df]
currently used in notebooks/04_dashboard_spa.ipynb:

1. Print both schemas side by side
2. Identify any column name or dtype mismatches
3. If there are mismatches, write a normalization function that transforms the real data
   to match the expected schema
4. Replace the generate_groundwater() synthetic call in the notebook with:
   - A try/except that loads from data/processed/[FILENAME].parquet
   - Falls back to generate_groundwater() if the file doesn't exist (so notebook still works fresh)
5. Verify the dashboard tab that uses this data still renders correctly by describing
   what the updated chart would show

Show me the exact cell changes needed.
```

---

### PROMPT: build-regression-model
```
USE WHEN: Ready to fit the OLS regression model on the real merged dataset
PASTE INTO: Claude Code
```

Read data/processed/facility_year.parquet (the master analytical dataset).

Fit the primary OLS regression model from the research plan:
water_gal_yr = b0 + b1*it_capacity_mw + b2*wue_l_per_kwh + b3*cooling_type_dummies
             + b4*pdsi + b5*cdd + error

Steps:
1. Print column availability — flag any predictors that are missing or have > 30% nulls
2. For missing predictors, suggest the best available substitute from the dataset
3. Create dummy variables for cooling_type (drop one category as reference)
4. Drop rows with any missing value in the model variables (log N dropped)
5. Run train/test split (80/20, random_state=42)
6. Fit OLS using statsmodels (not sklearn) — we need p-values and CIs for academic reporting
7. Print the full model summary table
8. Compute VIF for each predictor (flag > 5 as multicollinearity concern)
9. Plot: residuals vs. fitted, Q-Q plot, histogram of residuals
10. Predict on test set, compute R², MAE, and RMSE
11. Save model summary as text to reports/regression_summary.txt

If R² < 0.4, suggest 3 additional predictors that might improve fit given available data.
```

---

### PROMPT: run-scenario-model
```
USE WHEN: Ready to run formal scenario projections using the src/models/scenarios.py module
PASTE INTO: Claude Code
```

Read src/models/scenarios.py and data/processed/facility_year.parquet.

Run the full scenario analysis:
1. Extract the base_state from the most recent year of real data:
   - total_mw: sum of it_capacity_mw for operating facilities
   - mean_pue: mean PUE across operating facilities (weighted by MW)
   - mean_wue: mean WUE (weighted by MW, use industry default for undisclosed)
   - mean_renewable_pct: from iowa_grid_summary.parquet
   - grid_carbon_lbs_mwh: from iowa_grid_emission_factors.parquet (MROW)
2. Instantiate ScenarioModel with real base_state
3. Run all three scenarios to 2035
4. Run Monte Carlo with n=5000 (use 5000 for final analysis, 1000 for dev)
5. Save all outputs to data/exports/:
   - scenarios_projections.parquet (long format)
   - monte_carlo_summary.parquet (percentile bounds by year)
   - scenarios_2030_summary.csv (key milestone table for reporting)
6. Print the headline numbers: baseline 2030 water (billion gal) with 5th-95th CI

Then generate a "thesis finding" summary: one paragraph, 3-4 sentences,
stating the probable 2030 water and energy impact under baseline with uncertainty range.
```

---

### PROMPT: generate-export-figures
```
USE WHEN: Need publication-quality static figures for the thesis report
PASTE INTO: Claude Code
```

Generate publication-quality static figures (PNG, 300 DPI) for the thesis report.
Save all figures to reports/figures/.

Use matplotlib + seaborn, NOT plotly (static figures for PDF embedding).
Style: clean, minimal, colorblind-safe palette (use seaborn "colorblind" palette).
Font: Arial or Helvetica, minimum 11pt axis labels, 9pt tick labels.

Figures needed:
1. fig_01_iowa_dc_growth.png
   Line chart: Iowa DC total IT capacity (MW) by year 2015-2025
   Secondary axis: number of facilities
   Add vertical markers for major facility openings if data available

2. fig_02_water_by_county.png
   Horizontal bar chart: estimated annual water withdrawal (M gal/yr) by county
   Color by aquifer zone
   Include error bars if uncertainty estimates available

3. fig_03_scenario_projections.png
   Three-panel figure: water | electricity | CO2
   Each panel: three scenario lines + Monte Carlo 90% CI band on baseline
   Years 2024-2035, with 2023 actuals as anchor point

4. fig_04_pue_wue_scatter.png
   Scatter plot: PUE (x) vs. WUE (y), sized by IT capacity, colored by cooling type
   Add industry benchmark lines (PUE=1.5, WUE=1.8)
   Label disclosed hyperscaler facilities by operator name

5. fig_05_grid_mix_trends.png
   Stacked area chart: Iowa electricity generation by fuel type 2015-2025
   Overlay: data center electricity consumption as % of total (right axis)

For each figure, include a caption line at the bottom of the code: "CAPTION: [text]"
```

---

### PROMPT: validate-estimates
```
USE WHEN: Final QA pass before submitting or presenting results
PASTE INTO: Claude Code
```

Perform a validation pass on my Iowa data center impact estimates.

Read:
- data/processed/facility_year.parquet
- data/exports/scenarios_projections.parquet
- reports/regression_summary.txt (if it exists)

Validation checks:

1. SANITY CHECK — ORDER OF MAGNITUDE
   Compare my estimated total annual water withdrawal to:
   - Iowa DNR large water users list (top 50 industrial users — load from data/raw/dnr/)
   - Google's disclosed water use for its Iowa data centers (from their sustainability report)
   - The 25,000 gal/day permit threshold: how many facilities exceed this?

2. REGRESSION VALIDATION
   - For the 3-5 facilities where we have BOTH our estimate AND a company-disclosed figure,
     compute the % error: (estimate - disclosed) / disclosed
   - Flag any facility where our estimate is off by > 50%

3. SCENARIO BOUNDS CHECK
   - Does the 2030 baseline projection fall within the Monte Carlo 90% CI?
   - Is the High Growth scenario actually higher than Baseline across all years?
   - Does the Efficiency scenario cross below Baseline at the expected year?

4. UNIT CONSISTENCY
   - Confirm water is consistently in gallons throughout (not mixing gallons and liters)
   - Confirm electricity is consistently in MWh or TWh (not mixing)
   - Confirm CO2 is in metric tons (tCO2e) not short tons

Report each check as PASS / FAIL / FLAG with a one-line explanation.
For any FLAG, suggest what action to take.
```
