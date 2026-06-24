# API & MCP Resources
## Iowa Data Center Environmental Impact Study

---

## APIs Wired Into the Project

Implemented in `src/collectors/` or documented in `config.py`.

### EIA Open Data v2
- **URL:** https://www.eia.gov/opendata/
- **Registration:** https://www.eia.gov/opendata/register.php
- **Key variable:** `EIA_API_KEY` in `.env`
- **What it provides:** Iowa electricity generation by fuel type, retail sales by sector, installed capacity by fuel type
- **Endpoints used:**
  - `electricity/electric-power-operational-data/data/` — generation
  - `electricity/retail-sales/data/` — sales by sector
- **Rate limit:** None documented; retry on 429
- **Collector:** `src/collectors/eia_collector.py`
<img width="1793" height="971" alt="image" src="https://github.com/user-attachments/assets/3b76eb7f-eb2f-4a7d-b9e7-fd3c2ab60234" />
https://www.eia.gov/opendata/index.php#bulk-downloads

---

### USGS NWIS (National Water Information System)
- **URL:** https://waterdata.usgs.gov/nwis
- **API base:** https://waterservices.usgs.gov/nwis/
- **Key variable:** None — public API, no registration required
- **What it provides:** Iowa groundwater monitoring well sites, daily depth-to-water measurements, aquifer classifications
- **Endpoints used:**
  - `/site/` — monitoring site discovery (`stateCd=ia`, `siteType=GW`)
  - `/dv/` — daily values (`parameterCd=72019` = depth to water ft BLS)
- **Rate limit:** Polite use expected; add `time.sleep(1)` between bulk requests
- **Collector:** `src/collectors/usgs_groundwater.py`

---

### NOAA Climate Data Online (CDO) v2
- **URL:** https://www.ncdc.noaa.gov/cdo-web/
- **Registration:** https://www.ncdc.noaa.gov/cdo-web/token
- **Key variable:** `NOAA_CDO_TOKEN` in `.env` (Authorization header)
- **What it provides:** Iowa Palmer Drought Severity Index (PDSI), annual precipitation, cooling degree days
- **Endpoints used:**
  - `/cdo-web/api/v2/data/` — climate observations
  - Iowa FIPS location: `FIPS:19`
  - Dataset IDs: `ANNUAL` (drought), datatypes: `PDSI`, `PRCP`, `CLDD`
- **Rate limit:** Max 1,000 results per request; use `_paginate()` with offset stepping
- **Collector:** `src/collectors/noaa_climate.py`

---

### NOAA Drought Monitor (Direct CSV)
- **URL:** https://droughtmonitor.unl.edu/
- **CSV endpoint:** `https://droughtmonitor.unl.edu/DmData/GISData.aspx?mode=table&aoi=county&date=...`
- **Key variable:** None — public direct download
- **What it provides:** Weekly county-level drought classification (D0–D4), % area in each drought category
- **Usage:** Download as CSV, reshape to annual drought severity score per Iowa county
- **Collector:** `src/collectors/noaa_climate.py` → `get_drought_monitor_csv()`

---

### EPA eGRID (Emissions & Generation Resource Integrated Database)
- **URL:** https://www.epa.gov/egrid
- **Access:** Excel file download (annual release, ~50MB)
- **Key variable:** None — public download
- **What it provides:** MROW subregion carbon intensity (lbs CO2/MWh), Iowa grid emission factors, fuel mix by subregion
- **Iowa grid subregion:** `MROW` (Midwest Reliability Organization — West)
- **Usage:** Download Excel, filter to MROW rows, save emission factors to Parquet
- **Prompt:** See `prompts/01_data_elicitation.md` → `extract-epa-egrid`

---

### Iowa DNR Water Allocation Portal
- **URL:** https://programs.iowadnr.gov/nwis/
- **Large water users report:** https://www.iowadnr.gov/Environmental-Protection/Water-Quality/Water-Quantity/Water-Use-Data
- **Key variable:** None — manual download or scrape
- **What it provides:** Industrial water use permits, large water user list (top 50+ by withdrawal volume), permitted volumes by facility and aquifer
- **Permit threshold:** Facilities withdrawing > 25,000 gallons/day must report
- **Usage:** Download annual report PDF or CSV; cross-check DC water estimates against reported industrial withdrawals

---

### Nominatim (OpenStreetMap Geocoding)
- **URL:** https://nominatim.openstreetmap.org/search
- **Key variable:** None — public API
- **What it provides:** Lat/lon coordinates from facility addresses; reverse geocoding
- **Usage:** Geocode Iowa DC addresses to lat/lon for spatial analysis
- **Rate limit:** 1 request/second maximum — always add `time.sleep(1.1)` between calls
- **User-Agent:** Must set a descriptive `User-Agent` header per OSM policy
- **Prompt:** See `prompts/03_collection_workflow.md` → `geocode-batch`

---

### Data Center Map (Web Scrape)
- **URL:** https://www.datacentermap.com/usa/iowa/
- **Key variable:** None — public website
- **What it provides:** Iowa facility listings by operator, city, certifications, and specs
- **Access method:** `requests` + `BeautifulSoup`; use `Playwright` if JS-rendered
- **Rate limit:** Add `time.sleep(2)` between requests; check `robots.txt`
- **Prompt:** See `prompts/03_collection_workflow.md` → `scrape-datacentermap`

---

## Additional Reference Data Sources

These are downloaded manually or via direct URL — no API key required.

| Source | URL | What It Provides |
|--------|-----|-----------------|
| Iowa Geological Survey | https://www.iihr.uiowa.edu/igs/ | Aquifer maps, recharge rate estimates, IGS reports |
| EPA ECHO | https://echo.epa.gov/ | Facility-level environmental compliance and permit data |
| EPA Greenhouse Gas Reporting | https://www.epa.gov/ghgreporting | Large industrial GHG emitters by facility |
| Iowa State GIS Bureau | https://www.iowaonline.state.ia.us/gis/ | Iowa county shapefiles, land use layers |
| US Census ACS | https://data.census.gov/ | County population, housing, economic data |
| USDA NASS | https://www.nass.usda.gov/ | Agricultural water use by county |
| Lawrence Berkeley LBNL | https://datacenters.lbl.gov/ | National DC energy use reports and benchmarks |
| Uptime Institute | https://uptimeinstitute.com/ | Annual PUE/WUE industry benchmarks |
| Green Grid | https://www.thegreengrid.org/ | PUE/WUE/CUE methodology and benchmarks |

---

## MCP Connectors — Registry Available

These connectors are available in the Cowork MCP registry but not yet installed.
Install via **Settings → Plugins & Connectors**.

---

### Elicit
- **Registry URL:** https://elicit.com/api/mcp
- **Directory UUID:** `1287875c-308f-4a61-9ebf-4a0201ef214f`
- **What it provides:** Search and analyze scientific papers with structured extraction
- **Tools:** `search_papers`, `search_trials`, `create_report`, `list_reports`, `get_report`
- **Why useful for this project:** Directly supports literature collection workflow in `prompts/02_search_strategy.md`. Search for peer-reviewed papers on data center WUE, Iowa aquifer stress, DPSIR applications — get structured summaries with citations.
- **Recommended use:** Run `lit-search-academic` prompts through Elicit instead of web search for better citation quality and structured output

---

### Consensus
- **Registry URL:** *(via Cowork MCP registry)*
- **Directory UUID:** `65247229-f0c7-49df-9044-fcbb8b3894c6`
- **What it provides:** Evidence-based answers grounded in peer-reviewed research
- **Tools:** `search`
- **Why useful:** Quick "what does the science say about X" queries during hypothesis formation; good complement to Elicit for methodology validation

---

### Felt Maps
- **Registry URL:** https://felt.com/mcp
- **Directory UUID:** `9133bf4a-0a23-402b-8b04-bd0a400fd5f9`
- **What it provides:** Create, update, and analyze geospatial maps from data
- **Tools:** `create_map`, `get_map`, `update_map`, `get_map_layers`, `share_map` + 26 more
- **Why useful for this project:** Push `county_risk_ranking.csv` (Environmental Burden Score by county) directly into an interactive Iowa choropleth map without writing geopandas/plotly code. Share map URL for thesis appendix or presentation.
- **Recommended use:** After running the spatial hotspot analysis (`prompts/04_impact_measurement.md` → `spatial-hotspot-analysis`), upload results to a Felt map

---

### alphaXiv
- **Registry URL:** https://api.alphaxiv.org/mcp/v1
- **Directory UUID:** `fa0fb2e1-c3ba-4486-b576-0881432c5a4e`
- **What it provides:** Full-text search and access over arXiv preprints
- **Tools:** `full_text_papers_search`, `get_paper_content`, `embedding_similarity_search`, `agentic_paper_retrieval`
- **Why useful:** Cutting-edge data center energy/water modeling papers often appear on arXiv before peer-reviewed publication. Good for methodology papers on LCA, Monte Carlo uncertainty, and spatial analysis of industrial water use.

---

### Scite
- **Registry URL:** https://api.scite.ai/mcp
- **Directory UUID:** `f65118b4-06cb-468a-b2c7-f203ae7b54ea`
- **What it provides:** Literature search with citation context — shows whether papers support or contradict each other
- **Tools:** `search_literature`
- **Why useful:** Validates whether a methodology or claim you're citing has been supported or challenged in subsequent literature. Good for methodological defensibility checks before thesis submission.

---

### Scholar Gateway
- **Registry URL:** https://connector.scholargateway.ai/mcp
- **Directory UUID:** `ff091334-0f12-4d0e-a973-c00467dd3818`
- **What it provides:** Semantic search over scholarly research with citation generation
- **Tools:** `Semantic Search`
- **Why useful:** Alternative to Elicit for semantic (meaning-based) literature search when keyword search misses relevant papers

---

### Tavily
- **Registry URL:** https://mcp.tavily.com/mcp
- **Directory UUID:** `d8a25d2a-e5ea-4860-b990-277244df5417`
- **What it provides:** Web search, URL extraction, site crawling, deep research
- **Tools:** `tavily_search`, `tavily_extract`, `tavily_crawl`, `tavily_map`, `tavily_research`
- **Why useful:** Grey literature collection — crawl Iowa DNR pages, news archives, company sustainability report pages. Supports `prompts/02_search_strategy.md` → `lit-search-grey` and `search-facility-news`.

---

## Priority Install Order

| Priority | Connector | Reason |
|----------|-----------|--------|
| 1 | **Elicit** | Core literature workflow — search papers with structured output |
| 2 | **Felt Maps** | Instant Iowa county choropleth maps from CSV data |
| 3 | **Tavily** | Grey literature and facility news scraping with crawl capability |
| 4 | **alphaXiv** | Preprint access for cutting-edge methodology papers |
| 5 | **Scite** | Citation validation before thesis submission |

---

## Config Reference

All API keys and endpoints are managed in `config.py` and `.env`.
See `.env.example` for the full key template.

```python
# config.py — API dict
API = {
    "eia_key":    os.getenv("EIA_API_KEY"),
    "noaa_token": os.getenv("NOAA_CDO_TOKEN"),
    "dcbyte_key": os.getenv("DCBYTE_API_KEY"),   # optional
}
```

Never commit `.env` to git. It is listed in `.gitignore`.
