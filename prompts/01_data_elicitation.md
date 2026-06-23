# Data Elicitation Prompts
## Extract structured data from documents, reports, and disclosures

Use these prompts when you have a source in hand (PDF, URL, paste of text) and need
to pull specific numbers, metrics, or facts into a structured format for the dataset.

---

### PROMPT: extract-sustainability-report
```
USE WHEN: You have a hyperscaler (Google/Meta/Microsoft/AWS) sustainability or ESG report PDF
PASTE INTO: Claude (Cowork) — attach the PDF or paste extracted text
```

I am building an environmental impact dataset for Iowa data centers for a PhD research project.
I need you to extract all quantitative metrics from this sustainability report that relate to
data center operations.

For each metric found, return a structured table with these columns:
- metric_name (exact term used in the report)
- value (numeric only)
- unit (MWh, liters, m3, tCO2e, L/kWh, %, etc.)
- year (reporting year)
- scope (facility-level | company-wide | regional)
- geography (Iowa | US | global | [specific state/region])
- notes (any caveats, methodology notes, or data quality flags)

Specifically look for:
1. Power Usage Effectiveness (PUE) — by facility or region
2. Water Usage Effectiveness (WUE) — direct and indirect
3. Total water withdrawal and consumption (gallons or liters or m3)
4. Total electricity consumption (MWh or TWh)
5. Renewable energy percentage or MWh matched
6. Carbon / GHG emissions — Scope 1, 2, 3 (tCO2e)
7. Carbon Usage Effectiveness (CUE)
8. Facility count, locations, and IT capacity (MW)
9. Cooling technology descriptions
10. Any Iowa-specific or Midwest-specific data

If a metric is reported at company-wide level only and cannot be disaggregated to Iowa,
flag it with geography="global — not Iowa-specific" and note the methodology for
how it could be prorated (e.g., by Iowa MW share of total company MW).

Format output as a markdown table, then below it write a "Data Quality Assessment"
paragraph noting any gaps, inconsistencies, or limitations.

[PASTE REPORT TEXT OR ATTACH PDF BELOW]
```

---

### PROMPT: extract-permit-record
```
USE WHEN: You have an Iowa DNR water allocation permit or air permit document
PASTE INTO: Claude (Cowork)
```

Extract the following fields from this Iowa DNR permit document and return as a
structured JSON object:

```json
{
  "permit_number": "",
  "facility_name": "",
  "operator": "",
  "address": "",
  "county": "",
  "permit_type": "water_allocation | air_construction | air_operating | other",
  "aquifer_zone": "",
  "permitted_volume_gal_day": null,
  "permitted_volume_gal_year": null,
  "water_use_type": "industrial | commercial | municipal | agricultural",
  "issue_date": "",
  "expiration_date": "",
  "status": "active | expired | pending",
  "conditions": [],
  "notes": ""
}
```

If any field is not present in the document, set it to null and add a note explaining
what was searched for. Do not invent or infer values — only extract what is explicitly stated.

[PASTE PERMIT TEXT BELOW]
```

---

### PROMPT: extract-epa-egrid
```
USE WHEN: Working with the EPA eGRID Excel download to get Iowa grid emission factors
PASTE INTO: Claude Code
```

I have the EPA eGRID data file at data/raw/epa/eGRID2023_data.xlsx.
Read it with pandas and extract the Iowa grid emission factors.

Steps:
1. Load the SUBRGN sheet
2. Filter to the MROW subregion (Midwest Reliability Organization West) — this covers Iowa
3. Extract these columns: SUBRGN, SRCO2RTA (CO2 lbs/MWh), SRSO2RTA (SO2 lbs/MWh),
   SRNOXRTA (NOx lbs/MWh), SRCH4RTA (CH4 lbs/MWh), SRNTXRTA (non-baseload CO2 lbs/MWh)
4. Also load the historical SUBRGN data from any prior year tabs available
5. Return a DataFrame with year and all emission rate columns
6. Save to data/processed/iowa_grid_emission_factors.parquet
7. Print a summary showing how Iowa's grid carbon intensity has changed over available years

The goal is to track how Iowa's grid gets cleaner over time as wind generation grows,
which affects the Scope 2 emissions calculation for data centers.
```

---

### PROMPT: extract-company-report-table
```
USE WHEN: A PDF or webpage has a table of data you need structured
PASTE INTO: Claude (Cowork) — paste the raw table text
```

The following is raw text copied from a [COMPANY NAME] sustainability report table.
It contains messy formatting from PDF extraction. Please:

1. Reconstruct the intended table structure
2. Normalize all column headers to snake_case
3. Identify the unit for each numeric column and add a _unit suffix if mixed
4. Flag any cells where the value seems inconsistent with its neighbors (possible OCR error)
5. Return the clean table as both:
   a. A markdown table (for review)
   b. A Python dict I can pass to pd.DataFrame() (for import)

Table context: [BRIEF DESCRIPTION — e.g., "Annual PUE by data center region 2019-2023"]

[PASTE RAW TABLE TEXT BELOW]
```

---

### PROMPT: extract-usgs-site-metadata
```
USE WHEN: You have a USGS NWIS site page URL or response and need to classify the well
PASTE INTO: Claude (Cowork)
```

I am classifying Iowa groundwater monitoring wells for proximity to data center locations.
Review the following USGS NWIS site information and return:

1. site_no: USGS site number
2. site_name: full site name
3. county: Iowa county
4. aquifer_code: USGS aquifer code
5. aquifer_name: human-readable aquifer name (Jordan / Dakota / Alluvial / other)
6. depth_to_water_available: true/false (is parameter 72019 available?)
7. period_of_record: start date to end date
8. data_frequency: daily | monthly | sporadic
9. nearest_dc_county: [I will provide the county list — flag if this well is in a data center county]
10. relevance_score: high | medium | low — with one-sentence rationale

Data center counties of interest: Polk, Dallas, Linn, Black Hawk, Scott, Story, Johnson

[PASTE USGS SITE INFO BELOW]
```

---

### PROMPT: extract-news-facility-facts
```
USE WHEN: You have a news article about a new Iowa data center announcement
PASTE INTO: Claude (Cowork)
```

Extract facility facts from this news article about an Iowa data center.
Return ONLY what is explicitly stated in the article — do not infer or estimate.

Output fields:
- operator / company name
- facility location (address, city, county if mentioned)
- announced IT capacity (MW) — distinguish between Phase 1 and total planned
- announced investment ($)
- announced jobs (construction vs. permanent)
- construction start date (if mentioned)
- expected online date (if mentioned)
- cooling technology (if mentioned)
- water source (if mentioned)
- tax incentives (if mentioned — type and value)
- grid connection details (if mentioned)
- status: announced | under_construction | operating
- source URL (if you have it)
- publication date

Confidence flag for each field: HIGH (explicitly stated) | MEDIUM (implied) | LOW (inferred)

[PASTE ARTICLE TEXT BELOW]
```

---

### PROMPT: extract-drought-monitor-county
```
USE WHEN: You have downloaded a US Drought Monitor CSV and need to reshape it for analysis
PASTE INTO: Claude Code
```

I have a US Drought Monitor CSV at data/raw/noaa/iowa_drought_monitor.csv.
The file has weekly observations with columns for D0, D1, D2, D3, D4 (% area in each category)
and a ValidStart date column, plus county FIPS codes or county names.

Please:
1. Load the CSV and print the first 5 rows and column names
2. Filter to Iowa counties only
3. Parse ValidStart as datetime, create year and week_of_year columns
4. Compute an annual "drought severity score" per county:
   drought_score = D0*1 + D1*2 + D2*3 + D3*4 + D4*5
   This weights more severe drought categories higher
5. Compute annual mean drought_score and annual max drought_score per county
6. Merge with the county FIPS-to-name crosswalk (use the Census TIGER data or a simple dict)
7. Save to data/processed/iowa_drought_annual_county.parquet
8. Print the top 5 counties by 2023 drought severity score
```
