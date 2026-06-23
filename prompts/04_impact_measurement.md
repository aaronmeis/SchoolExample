# Impact Measurement Prompts
## Quantify, frame, and analyze environmental impacts

Use these prompts to move from raw data to meaningful impact statements.
They cover DPSIR framing, LCA attribution, scenario interpretation, and
generating thesis-ready quantitative claims.

---

### PROMPT: dpsir-framing
```
USE WHEN: Writing the conceptual framework section or need to frame a finding in DPSIR terms
PASTE INTO: Claude (Cowork)
```

I am applying the DPSIR framework (Drivers - Pressures - State - Impacts - Responses)
to analyze the environmental effects of data center growth in Iowa.

Given the following data point or finding:
[PASTE YOUR DATA FINDING HERE — e.g., "Iowa data centers are estimated to withdraw
450 million gallons of groundwater per year, up from 80 million gallons in 2018"]

Help me:
1. Classify which DPSIR component this finding belongs to (and why)
2. Map the causal chain: what are the upstream Drivers causing this Pressure?
   What State variable is changing? What are the resulting Impacts?
3. Identify what Response mechanisms exist or could be enacted
4. Suggest what additional data I would need to strengthen the causal chain
5. Write a 3-4 sentence DPSIR framing paragraph I could use in my thesis introduction
6. Identify the key uncertainty in this causal chain that my research could help reduce

Use Iowa-specific context: agricultural water competition, Jordan Aquifer recharge rates,
MidAmerican Energy's renewable buildout, Linn County zoning precedent.
```

---

### PROMPT: water-impact-statement
```
USE WHEN: Need to write a defensible quantitative water impact finding
PASTE INTO: Claude (Cowork)
```

Help me write a defensible, quantified water impact statement for Iowa data centers
based on the following inputs:

Current data:
- Estimated total Iowa data center IT capacity: [X] MW (operating) + [Y] MW (under construction)
- Mean WUE for Iowa DCs: [Z] L/kWh (based on [N] facilities with disclosed data)
- Assumed WUE for undisclosed facilities: [W] L/kWh (industry median/default)
- Iowa grid carbon intensity: [C] lbs CO2/MWh (MROW, most recent eGRID)
- Iowa statewide industrial water use: [T] billion gallons/year (Iowa DNR)
- Jordan Aquifer estimated annual recharge: [R] billion gallons/year (IGS)

Calculate and frame:
1. Total estimated annual direct water withdrawal (gallons and as % of state industrial use)
2. Total estimated annual indirect water use (power plant cooling water, using indirect WUE formula)
3. Combined direct + indirect water footprint
4. Water withdrawal as % of Jordan Aquifer estimated annual recharge
5. County-level hotspots (Polk, Linn, Dallas counties — show their share)
6. Year-2030 projection under baseline scenario (500 MW/yr growth, WUE improving at -0.05/yr)

For each calculation:
- Show the formula used
- Show the calculation with actual numbers
- Note assumptions and their sensitivity
- State the confidence level: HIGH | MEDIUM | LOW

Write a 2-paragraph "probable finding" statement I could put in my thesis:
"Under [scenario], Iowa data centers are estimated to withdraw approximately [X] billion
gallons of groundwater annually by [year], representing [Y]% of..."
```

---

### PROMPT: energy-impact-statement
```
USE WHEN: Need to quantify and frame electricity and carbon impact
PASTE INTO: Claude (Cowork)
```

Help me calculate and frame the energy and carbon impact of Iowa data centers.

Inputs:
- Total Iowa DC IT capacity: [X] MW
- Mean PUE: [Y]
- Iowa grid renewable %: [Z]% (current year)
- Iowa eGRID MROW carbon intensity: [C] lbs CO2/MWh
- Iowa total electricity consumption: [T] TWh/year (EIA)
- Iowa grid total generation capacity: [G] GW

Calculate:
1. Total estimated annual electricity consumption of Iowa DCs (MWh, TWh)
2. As % of Iowa total electricity consumption
3. Location-based Scope 2 GHG emissions (tCO2e) — using MROW grid factor
4. Market-based Scope 2 estimate (if corporate renewable PPAs apply)
5. Equivalent: how many Iowa households' annual electricity use does this represent?
6. Grid capacity factor impact: what % of Iowa peak grid capacity do DCs represent?
7. Carbon trajectory: if DCs grow at 500 MW/yr and grid decarbonizes at [rate],
   does absolute carbon increase or decrease by 2030?

Also frame the "rebound risk": even if efficiency (PUE) improves, does total energy use
still grow due to capacity additions? Quantify the crossover point.

Write a 3-sentence headline finding for the energy section of my thesis.
```

---

### PROMPT: spatial-hotspot-analysis
```
USE WHEN: Need to identify and describe geographic concentration of impacts
PASTE INTO: Claude Code
```

Using the county_year master table at data/processed/county_year.parquet,
perform a spatial hotspot analysis to identify the Iowa counties at highest
cumulative environmental risk from data center growth.

Steps:
1. Load the county_year table, filter to most recent year available
2. Compute a normalized "environmental burden score" (EBS) for each county:
   EBS = (water_gal_yr / state_total_water) * 0.40
       + (electricity_twh / state_total_elec) * 0.30
       + (scope2_tco2e / state_total_co2) * 0.20
       + (n_facilities / state_total_n) * 0.10
   (weights reflect water scarcity priority in Iowa context)
3. Also compute an "aquifer stress index" (ASI):
   ASI = (dc_water_gal_yr / county_total_water_gal_yr) * drought_score_normalized
   where drought_score comes from iowa_drought_annual_county.parquet
4. Rank all counties by EBS descending
5. Classify top quartile as "High Risk", second quartile as "Moderate Risk"
6. Generate a summary table: county | n_facilities | total_mw | water_mgal_yr | EBS | ASI | risk_tier
7. Save to data/exports/county_risk_ranking.csv

Print the top 5 counties with a one-sentence interpretation for each
explaining WHY that county scores high (is it water, energy, concentration, or drought?).

This output will drive the choropleth map in the Spatial tab of the dashboard.
```

---

### PROMPT: scenario-interpretation
```
USE WHEN: The scenario model has run and you need to interpret outputs for writing
PASTE INTO: Claude (Cowork)
```

I have run three scenarios for Iowa data center impacts through 2035.
Here are the key output values:

BASELINE (500 MW/yr growth, PUE -0.02/yr, WUE -0.05/yr, 80% renewables by 2030):
- 2030 water withdrawal: [X] billion gallons/year
- 2030 electricity: [Y] TWh/year
- 2030 CO2e: [Z] thousand metric tons/year

HIGH GROWTH (900 MW/yr, no efficiency improvement, 65% renewables):
- 2030 water withdrawal: [X2] billion gallons/year
- 2030 electricity: [Y2] TWh/year
- 2030 CO2e: [Z2] thousand metric tons/year

EFFICIENCY + POLICY (400 MW/yr, PUE -0.04/yr, WUE -0.10/yr, 95% renewables, -15% water permits):
- 2030 water withdrawal: [X3] billion gallons/year
- 2030 electricity: [Y3] TWh/year
- 2030 CO2e: [Z3] thousand metric tons/year

MONTE CARLO (baseline, 3000 runs): 90% CI for 2030 water = [LOW] to [HIGH] billion gal/yr

Help me:
1. Calculate the range between scenarios as % difference from baseline
2. Identify which metric shows the greatest scenario sensitivity (water vs. energy vs. carbon)
3. Write a 4-sentence "probable prediction" paragraph framing the baseline finding
4. Write a 2-sentence "policy lever" sentence identifying the most impactful intervention
5. Identify the key assumption that most drives uncertainty in the baseline projection
6. Suggest how I would validate these projections against real data as it becomes available

Use specific Iowa context: Jordan Aquifer recharge, MidAmerican Energy wind targets,
proposed legislative caps on industrial water permits.
```

---

### PROMPT: lca-attribution
```
USE WHEN: Need to attribute water or carbon impacts to specific stages of the data center lifecycle
PASTE INTO: Claude (Cowork)
```

I need to apply a simplified Life Cycle Assessment (LCA) framework to disaggregate
the environmental impacts of Iowa data centers by lifecycle stage.

For a representative Iowa hyperscale data center with:
- IT capacity: [MW]
- PUE: [X]
- WUE: [Y] L/kWh
- Cooling type: [evaporative/liquid/air]
- Expected operational life: 20 years

Estimate the relative contribution of each stage to total lifecycle impacts:

STAGES:
1. Construction: steel, concrete, copper, server manufacturing (embodied carbon)
2. Operation - direct: on-site cooling water, backup generator fuel, refrigerants
3. Operation - indirect electricity: Scope 2 (grid carbon × kWh consumed)
4. Operation - indirect water: power plant cooling water per kWh of grid electricity
5. End of life: server disposal, building demolition

For each stage:
- Rough % of total lifecycle water footprint
- Rough % of total lifecycle carbon footprint
- Primary data source or literature reference for the estimate
- Key uncertainty range

Note which stages are covered by my current dataset and which are gaps.
Suggest whether a full LCA is needed for PhD-level rigor or whether operational impacts
alone are sufficient given my research questions.

Conclude with: which 2 stages should I prioritize for data collection given the
likely magnitude of impact and data availability in Iowa?
```

---

### PROMPT: comparative-benchmarking
```
USE WHEN: Need to contextualize Iowa findings against comparable states or national averages
PASTE INTO: Claude (Cowork — web search enabled)
```

Help me put Iowa data center environmental impacts in comparative context.

My Iowa estimates:
- Total DC water withdrawal: [X] M gal/yr
- Total DC electricity: [Y] TWh/yr
- Mean PUE: [Z]
- Mean WUE: [W] L/kWh

Search for and provide comparable figures for:
1. Virginia (Northern Virginia — largest US DC market)
2. Texas (second largest and fastest growing)
3. Oregon / Washington (Pacific Northwest — major hyperscale concentration)
4. Arizona (Phoenix area — water-stressed DC market for comparison)
5. National US total (Lawrence Berkeley LBNL estimates)

For each comparison:
- Total DC water use (if published)
- Total DC electricity use
- State's water stress level (WRI Aqueduct score or similar)
- What % of state electricity DCs represent
- Regulatory environment (water restrictions, renewable mandates)

Then help me frame Iowa's position:
- Is Iowa's water situation more or less stressed than comparable states?
- Does Iowa's wind energy advantage offset its water intensity?
- What makes Iowa's situation distinctive enough to warrant PhD-level research?

This will feed the "Significance of Iowa Case Study" section of my thesis proposal.
```
