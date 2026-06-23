# Search Strategy Prompts
## Find academic literature, datasets, government reports, and news

Use these prompts to surface relevant materials before building a dataset dimension
or writing a section of the research. Designed to be run in Claude with web search enabled.

---

### PROMPT: lit-search-academic
```
USE WHEN: Starting a new research dimension and need peer-reviewed sources
PASTE INTO: Claude (Cowork — web search enabled)
```

I am a PhD candidate researching the environmental impacts of data centers in Iowa.
I need peer-reviewed academic sources on the following topic:

TOPIC: [e.g., "groundwater depletion from data center cooling water use in the US Midwest"]

Search for and summarize the 8-10 most relevant academic papers or reports from the
last 10 years (2015-2025). For each source return:

1. Full citation (APA format)
2. DOI or URL
3. 2-3 sentence summary of key findings relevant to Iowa data centers
4. Key quantitative findings (numbers I could use in my analysis)
5. Methodological approach (regression / GIS / case study / survey / LCA / other)
6. Geographic scope (Iowa / Midwest / US / global)
7. Data sources used in the study
8. Relevance rating: HIGH | MEDIUM | LOW — with one-sentence rationale
9. Citation count if available (proxy for influence)

Also note:
- Any studies that specifically analyze Iowa, the Jordan Aquifer, or MISO/MROW grid
- Any studies that provide PUE or WUE benchmarks by cooling technology type
- Any open datasets cited that I should download

Format as a numbered reference list with the summary fields indented under each entry.
At the end, suggest 3 additional search terms I should try to broaden coverage.
```

---

### PROMPT: lit-search-grey
```
USE WHEN: Need government reports, industry whitepapers, NGO studies, or investigative journalism
PASTE INTO: Claude (Cowork — web search enabled)
```

Search for grey literature (government reports, industry whitepapers, investigative journalism,
NGO studies) on [TOPIC — e.g., "Iowa data center water use and groundwater impacts"].

Target sources:
- Iowa DNR annual reports and water use summaries
- Iowa Geological Survey aquifer assessments
- Lawrence Berkeley National Laboratory data center energy reports
- Uptime Institute annual global data center survey
- Green Grid PUE/WUE benchmark reports
- US DOE Office of Cybersecurity, Energy Security, and Emergency Response (CESER)
- EPA reports on industrial water use
- Investigative pieces from Iowa Capital Dispatch, Des Moines Register, or ProPublica
- Environmental advocacy reports from Iowa Environmental Council
- TAI (Technology Association of Iowa) economic impact studies

For each source found:
1. Title, author/org, date, URL
2. Key findings relevant to my Iowa data center research
3. Any data tables or datasets linked or embedded
4. Whether the source is primary (original data) or secondary (synthesizes others)
5. Potential bias or limitation to note

Prioritize sources with downloadable data or that cite specific Iowa facilities.
```

---

### PROMPT: search-open-datasets
```
USE WHEN: Looking for downloadable datasets for a specific variable
PASTE INTO: Claude (Cowork — web search enabled)
```

I need publicly available datasets for the following variable in my Iowa data center
environmental impact study:

VARIABLE: [e.g., "annual groundwater withdrawal by industrial sector, Iowa, 2010-2024"]

Search for datasets from:
1. USGS (waterdata.usgs.gov, sciencebase.gov)
2. EPA (epa.gov/egrid, echo.epa.gov, ghgreporting)
3. EIA (eia.gov/opendata)
4. NOAA/NCEI (ncdc.noaa.gov)
5. Iowa DNR (iowadnr.gov)
6. Iowa Geological Survey (iihr.uiowa.edu/igs)
7. USDA (nass.usda.gov, ers.usda.gov)
8. US Census Bureau (census.gov)
9. Iowa State University Extension (extension.iastate.edu)
10. Zenodo, Harvard Dataverse, or other academic repositories

For each dataset found:
- Name and description
- URL and access method (API / direct download / request)
- Temporal coverage (years available)
- Spatial resolution (county / HUC8 watershed / state / well-level)
- File format (CSV / JSON / shapefile / raster / Excel)
- License (public domain / CC / restricted)
- Variables included most relevant to my need
- Whether it can be joined to facility data via county FIPS or lat/lon

Rank by: most directly usable for Iowa county-level analysis first.
```

---

### PROMPT: search-facility-news
```
USE WHEN: Need to find recent Iowa data center announcements and track proposed projects
PASTE INTO: Claude (Cowork — web search enabled)
```

Search for news articles and press releases about data center development in Iowa
published in the last 18 months. I need to track proposed, under-construction, and
newly operating facilities for my research dataset.

For each facility announcement found, extract:
- Company / operator name
- Location (city, county)
- Announced MW capacity (Phase 1 and total if mentioned)
- Investment amount ($)
- Status: announced / permitted / under construction / operating
- Any environmental details (water source, cooling type, renewable energy commitments)
- Source URL and publication date

Also search for:
- Iowa legislative or regulatory actions affecting data centers (tax incentives, zoning, water permits)
- Any Iowa DNR water permit applications by data center operators
- Any community opposition or environmental review proceedings
- Linn County data center zoning rules and any updates

Organize results into a table: Operating | Under Construction | Proposed | Regulatory Actions
```

---

### PROMPT: search-benchmark-data
```
USE WHEN: Need industry benchmark values for PUE, WUE, or other efficiency metrics
PASTE INTO: Claude (Cowork — web search enabled)
```

I need current industry benchmark values for data center efficiency metrics to use
as comparison baselines in my Iowa environmental impact analysis.

Search for the most recent published benchmarks for:

1. Power Usage Effectiveness (PUE)
   - Global average PUE (by year, 2018-2024)
   - Hyperscale vs. enterprise vs. colocation PUE benchmarks
   - PUE by cooling technology (air-cooled vs. evaporative vs. liquid)
   - Iowa / Midwest climate advantage for PUE (free cooling hours)

2. Water Usage Effectiveness (WUE)
   - Global average WUE by cooling type
   - WUE for Google, Meta, Microsoft Iowa facilities specifically
   - WUE for evaporative cooling towers in humid continental climate (Iowa's climate)
   - Indirect WUE (power plant cooling water attribution per MWh)

3. Carbon Usage Effectiveness (CUE)
   - Industry average CUE trends 2018-2024
   - CUE for MROW grid subregion vs. national average

Sources to check: Uptime Institute Annual Survey, Green Grid, Lawrence Berkeley LBNL,
IEA Data Centres report, company sustainability reports (Google, Meta, Microsoft).

Return benchmarks as a structured table with source, year, and confidence level.
Note whether each benchmark is median, mean, best-in-class, or industry average.
```

---

### PROMPT: search-aquifer-literature
```
USE WHEN: Need specific hydrogeological context for Iowa aquifers targeted by data centers
PASTE INTO: Claude (Cowork — web search enabled)
```

Search for scientific and government literature on Iowa aquifer systems, specifically
focused on aquifers at risk from large industrial water users like data centers.

Key aquifers to research:
1. Jordan Aquifer (Cambrian-Ordovician) — deep, confined, used heavily in central Iowa
2. Dakota Aquifer (Cretaceous) — used in northwest Iowa
3. Alluvial aquifers — shallow, used near rivers (Des Moines, Cedar, Iowa rivers)
4. Galena-Platteville (Silurian-Devonian) — northeast Iowa

For each aquifer, find:
- Current recharge rates vs. extraction rates (sustainability assessment)
- Historical water level trends (ft/year decline or recovery)
- Major industrial users and their permitted volumes
- Proximity to known or proposed data center locations
- Any USGS or IGS monitoring well data showing stress
- Drought sensitivity (does the aquifer respond to surface drought?)
- Any existing or proposed pumping restrictions

Also find:
- Iowa DNR groundwater assessment reports
- Iowa Geological Survey aquifer maps (GIS layers if available)
- Academic studies on aquifer depletion in Iowa
- Any documented conflicts between agricultural and industrial water users

This will feed into the "aquifer stress index" layer of my spatial analysis.
```
