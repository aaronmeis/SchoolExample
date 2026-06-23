# Data Collection Workflow Prompts
## Systematic API pulling, scraping, geocoding, and manual entry

Use these prompts to set up, run, and troubleshoot data collection workflows.
All Claude Code prompts assume you are in the `iowa-dc-impact/` project directory.

---

### PROMPT: setup-api-collection
```
USE WHEN: Starting a new API data pull for the first time
PASTE INTO: Claude Code
```

I need to collect [DATASET NAME — e.g., "Iowa groundwater levels from USGS NWIS"] for my
Iowa data center environmental impact research project.

My project structure is:
- src/collectors/ — data collector modules
- data/raw/ — raw API responses saved as JSON or CSV
- data/processed/ — cleaned DataFrames as Parquet
- config.py — API keys and paths

Please:
1. Check if src/collectors/[RELEVANT_FILE].py already exists and read it
2. Run the collector for [DATE RANGE — e.g., "2018-01-01 to 2024-12-31"]
3. Inspect the raw response: print shape, dtypes, first 5 rows, and any obvious issues
4. Check for missing values in key columns and report counts
5. Save the raw response to data/raw/[FOLDER]/ with a timestamped filename
6. Tell me what the next processing step should be

If the API returns an error, show me the full error response and suggest 3 likely causes
(wrong endpoint, missing parameter, rate limit, auth issue).
```

---

### PROMPT: build-facility-table
```
USE WHEN: Building the core facility dataset from manual sources + scraping
PASTE INTO: Claude Code
```

I need to build the master Iowa data center facility table at
data/raw/facilities/iowa_dc_facilities.csv.

The schema is defined in config.py. Key fields:
facility_id, operator, county, aquifer_zone, status, cooling_type,
it_capacity_mw, pue, wue_l_per_kwh, year_online, lat, lon, tax_incentive_usd

Steps:
1. Check if data/raw/facilities/iowa_dc_facilities.csv exists — if so, show its current contents
2. Show me the current row count and which operators are covered
3. I will paste new facility data below — add it to the table, deduplicating on facility_id
4. Geocode any rows where lat/lon are missing using the Nominatim API
   (rate limit: 1 request/second, add time.sleep(1.1) between calls)
5. Validate: check that all lat values are between 40.3 and 43.7 (Iowa bounds)
   and all lon values are between -96.7 and -90.1
6. Save the updated CSV and print a summary: total rows, operators, MW by status

New facility data to add:
[PASTE NEW FACILITY DATA HERE — can be JSON, CSV row, or plain text description]
```

---

### PROMPT: geocode-batch
```
USE WHEN: You have a list of facility addresses that need lat/lon coordinates
PASTE INTO: Claude Code
```

I have a list of Iowa data center addresses that need geocoding.
Use the Nominatim API (https://nominatim.openstreetmap.org/search) — it's free with
a 1 request/second rate limit. Add time.sleep(1.1) between each request.

For each address:
1. Try the full address first: "ADDRESS, Iowa, USA"
2. If no result, try "CITY, Iowa, USA" as a fallback (county-centroid level)
3. Record the result_type: "address" | "city_fallback" | "failed"
4. Extract lat, lon, and display_name from the response
5. Flag any result where the returned county differs from our expected county

Addresses to geocode:
[PASTE LIST OF ADDRESSES — one per line, format: facility_id | address | expected_county]

Save results to data/processed/geocoded_facilities.csv and print a summary:
- N successfully geocoded at address level
- N geocoded at city fallback level
- N failed (list them)
```

---

### PROMPT: pull-eia-iowa
```
USE WHEN: Need to refresh Iowa electricity generation data from EIA
PASTE INTO: Claude Code
```

Pull the latest Iowa electricity data from the EIA Open Data API v2.
API key is in config.py (API["eia_key"]) and .env (EIA_API_KEY).

Collect three datasets:
1. Annual electricity generation by fuel type (src/collectors/eia_collector.py → get_iowa_generation)
2. Annual retail electricity sales by sector (get_iowa_retail_sales)
3. Annual installed capacity by fuel type (get_iowa_capacity)

For each:
- Pull from 2015 to present
- Save raw JSON to data/raw/eia/
- Print the shape and a quick pivot showing Iowa's fuel mix by year (% wind, % coal, % gas, % nuclear, % solar)

Then compute and save to data/processed/iowa_grid_summary.parquet:
- total_generation_mwh per year
- renewable_pct (wind + solar + hydro) per year
- carbon_intensity_lbs_mwh (join with eGRID MROW factor)
- commercial_sector_mwh per year (proxy for data center load growth)

Print the year-over-year change in renewable_pct to show the wind buildout trend.
```

---

### PROMPT: pull-usgs-groundwater
```
USE WHEN: Need to refresh Iowa groundwater level data from USGS NWIS
PASTE INTO: Claude Code
```

Pull Iowa groundwater monitoring data from the USGS NWIS API.
No API key needed. Use src/collectors/usgs_groundwater.py.

Steps:
1. First, call get_iowa_monitoring_sites() to get the current list of active Iowa wells
2. Print a summary: total sites, sites by county, sites with 10+ years of record
3. Filter to wells in these priority counties: Polk, Dallas, Linn, Black Hawk, Scott, Story, Johnson
   (these are the counties with current or proposed data center activity)
4. For priority county wells, call get_iowa_groundwater_levels(start_date="2010-01-01")
5. Compute the annual_trend() for each site (year-over-year water level change)
6. Flag any wells showing > 1.0 ft/year decline (aquifer stress signal)
7. Save to data/processed/iowa_gw_annual_trend.parquet

Print a table: site_no | county | aquifer | mean_depth_ft | trend_ft_yr | stress_flag
Sort by worst trend first.
```

---

### PROMPT: merge-all-sources
```
USE WHEN: All individual collectors have run and you need to build the master analytical dataset
PASTE INTO: Claude Code
```

I need to merge all collected data sources into a single analytical dataset.
All processed Parquet files are in data/processed/.

Build two master tables:

TABLE 1: facility_year (facility × year observations)
- Base: data/processed/facilities.parquet (or iowa_dc_facilities.csv)
- Add: iowa_grid_summary.parquet — join on year (add renewable_pct, carbon_intensity)
- Add: iowa_drought_annual_county.parquet — join on county + year (add pdsi, drought_score)
- Add: iowa_gw_annual_trend.parquet — join on county + year (add mean_depth_ft, trend_ft_yr)
- Compute: electricity_mwh_yr, water_gal_yr, scope2_tco2e using normalizer.py formulas

TABLE 2: county_year (county × year aggregates)
- Aggregate TABLE 1 by county + year: sum(MW), sum(water_gal_yr), sum(scope2_tco2e), mean(pue), mean(wue)
- Add: county population (Census ACS) if available
- Add: county agricultural water use from Iowa DNR large water users report if available
- Compute: dc_water_as_pct_county_total (if ag water data available)

After merging:
1. Print shape and completeness report (% non-null for each key column)
2. Identify the 5 county-years with highest estimated water withdrawal
3. Save both tables as Parquet to data/processed/
4. Save a CSV export of TABLE 2 to data/exports/ for use in the dashboard

Flag any join where < 80% of facility-years have climate/groundwater data (coverage gap).
```

---

### PROMPT: scrape-datacentermap
```
USE WHEN: Need to update the facility list from Data Center Map
PASTE INTO: Claude Code
```

Scrape the current Iowa data center listings from Data Center Map.
URL: https://www.datacentermap.com/usa/iowa/

Use requests + BeautifulSoup (not Playwright unless the page is JS-rendered).
If the page requires JavaScript, use Playwright: playwright install chromium first.

Extract for each listed facility:
- name
- city
- operator / company
- certifications (if listed)
- page URL for detail page

Then for each facility detail page, extract:
- address
- total space / power (if listed)
- year opened (if listed)
- any technical specs

Deduplicate against data/raw/facilities/iowa_dc_facilities.csv by name + city.
Print: N new facilities found | N already in dataset | N total after merge
Save new entries (not yet in dataset) to data/raw/facilities/dcmap_new_entries.csv
for manual review before adding to the master file.

Add time.sleep(2) between requests. Respect robots.txt.
```
