# Iowa Data Center Environmental Impact Study

A PhD-precursor research project quantifying the water, energy, and land-use
impacts of data center growth in Iowa. Built for a Python data science course
using the DPSIR (Drivers → Pressures → State → Impacts → Responses) framework.

---

## Project Overview

Iowa has emerged as a top-5 US data center market driven by tax incentives,
renewable energy infrastructure, and favorable climate. This project measures
the environmental burden of that growth across three dimensions:

| Dimension | Key Metric | Primary Data Sources |
|-----------|-----------|----------------------|
| Water / Groundwater | WUE (L/kWh), aquifer drawdown | USGS NWIS, Iowa DNR, NOAA drought |
| Energy & Emissions | PUE, MWh/yr, tCO2e/yr | EIA Open Data v2, EPA eGRID MROW |
| Land / Spatial | County hotspot index | Iowa GIS, Census ACS |

---

## Quick Start

### 1. Clone and configure

```bash
git clone <repo-url>
cd iowa-dc-impact
cp .env.example .env
# Edit .env and add your API keys:
#   EIA_API_KEY    → https://www.eia.gov/opendata/register.php
#   NOAA_CDO_TOKEN → https://www.ncdc.noaa.gov/cdo-web/token
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Run the dashboard (synthetic data — no API keys needed)

```bash
panel serve notebooks/04_dashboard_spa.ipynb --show
```

The dashboard opens at `http://localhost:5006` with 7 interactive tabs.
All tabs work out of the box using generated synthetic data that matches the
real data schema — swap in real data with one-line changes (see below).

### 4. Collect real data

Run collectors in order:

```bash
# Iowa electricity generation & grid mix
python -c "from src.collectors.eia_collector import EIACollector; ..."

# Iowa groundwater levels (no API key required)
python -c "from src.collectors.usgs_groundwater import USGSGroundwaterCollector; ..."

# Iowa climate / drought index
python -c "from src.collectors.noaa_climate import NOAAClimateCollector; ..."
```

See `prompts/03_collection_workflow.md` for ready-to-paste Claude Code prompts
that run each collector and handle errors.

### 5. Run scenario projections

```python
from src.models.scenarios import ScenarioModel
model = ScenarioModel(base_year=2023, end_year=2035)
results = model.run_all()
mc = model.monte_carlo("baseline", n=5000)
```

---

## Project Structure

```
iowa-dc-impact/
├── .env.example              # API key template (never commit .env)
├── config.py                 # Paths, API keys, constants, scenario params
├── requirements.txt          # All Python dependencies
│
├── data/
│   ├── raw/                  # Unmodified API responses (JSON, CSV)
│   │   ├── eia/
│   │   ├── usgs/
│   │   ├── noaa/
│   │   ├── facilities/       # iowa_dc_facilities.csv (master facility list)
│   │   └── dnr/
│   ├── processed/            # Cleaned DataFrames (.parquet)
│   └── exports/              # CSV exports for reporting and dashboard
│
├── src/
│   ├── collectors/           # Data ingestion modules
│   │   ├── eia_collector.py      # EIA Open Data v2 — Iowa electricity
│   │   ├── usgs_groundwater.py   # USGS NWIS — Iowa groundwater levels
│   │   └── noaa_climate.py       # NOAA CDO — drought index, precipitation
│   ├── processors/
│   │   └── normalizer.py         # PUE / WUE / CUE / scope2 calculations
│   └── models/
│       └── scenarios.py          # Baseline / High Growth / Efficiency projections
│                                 # + Monte Carlo uncertainty (scipy.stats)
│
├── notebooks/
│   └── 04_dashboard_spa.ipynb    # Panel SPA — 7-tab interactive dashboard
│
├── reports/
│   ├── figures/              # Publication-quality PNGs (300 DPI)
│   └── regression_summary.txt
│
└── prompts/                  # Copy-paste prompt library
    ├── 00_index.md               # Prompt index and usage guide
    ├── 01_data_elicitation.md    # Extract data from PDFs and reports
    ├── 02_search_strategy.md     # Find literature and datasets
    ├── 03_collection_workflow.md # Run API collectors and build facility table
    ├── 04_impact_measurement.md  # Quantify and frame environmental impacts
    ├── 05_claude_code_iteration.md # Python coding and debugging prompts
    ├── 06_literature_synthesis.md  # Write thesis-quality prose from sources
    └── 07_validation_qa.md         # Sanity checks and pre-submission audit
```

---

## Dashboard Tabs

| Tab | What It Shows |
|-----|--------------|
| 1 — Overview | Iowa DC growth summary: MW capacity, facility count, status map |
| 2 — Water | WUE distribution, annual water withdrawal by county, aquifer stress |
| 3 — Energy | PUE trends, electricity consumption, Iowa grid fuel mix over time |
| 4 — Spatial | Choropleth map — environmental burden score by county |
| 5 — Scenarios | Baseline / High Growth / Efficiency projections to 2035 with Monte Carlo CI |
| 6 — Regression | OLS and Random Forest models: coefficients, p-values, feature importance |
| 7 — Export | Filterable data table + CSV download |

Serving options:
```bash
# Inline in JupyterLab
jupyter lab notebooks/04_dashboard_spa.ipynb

# Standalone server
panel serve notebooks/04_dashboard_spa.ipynb --show --port 5006
```

---

## Swapping Synthetic Data for Real Data

Each synthetic data generator function (e.g., `generate_groundwater()`) produces
a DataFrame with the same schema as the real collector output. To use real data,
replace the generator call with a `pd.read_parquet()` call in the notebook:

```python
# Before (synthetic)
gw_df = generate_groundwater(n_sites=18)

# After (real data)
gw_df = pd.read_parquet("../data/processed/iowa_gw_annual_trend.parquet")
```

Use the `add-real-data-swap` prompt in `prompts/05_claude_code_iteration.md`
to let Claude Code do the schema alignment and replacement automatically.

---

## Key Constants

| Constant | Value | Notes |
|----------|-------|-------|
| `HRS` | 8,760 hrs/yr | Hours per year |
| `CF` | 0.85 | IT load capacity factor |
| `L2G` | 0.264172 | Liters to gallons |
| `PUE_EXCELLENT` | 1.2 | Best-in-class PUE |
| `PUE_GOOD` | 1.5 | Solid enterprise PUE |
| `WUE_EXCELLENT` | 1.0 L/kWh | Best-in-class WUE |
| `WUE_GOOD` | 1.8 L/kWh | Industry median WUE |
| Iowa grid region | MROW | EPA eGRID subregion |
| Iowa FIPS | 19 | Federal state code |

All constants defined in `config.py`.

---

## API Keys Required

| API | Key Variable | Registration |
|-----|-------------|-------------|
| EIA Open Data v2 | `EIA_API_KEY` | https://www.eia.gov/opendata/register.php |
| NOAA CDO | `NOAA_CDO_TOKEN` | https://www.ncdc.noaa.gov/cdo-web/token |
| USGS NWIS | *(none)* | Public API, no key needed |
| Iowa DNR Water Portal | *(manual download)* | https://programs.iowadnr.gov/nwis |

---

## Prompts Library

The `prompts/` folder contains 50+ copy-paste prompts organized by workflow stage.
See `prompts/00_index.md` for the full index and usage guide.

Quick reference — use these prompts in:
- **Claude (Cowork)** — research, literature synthesis, impact framing, writing
- **Claude Code** (`cd iowa-dc-impact && claude`) — API collection, data processing,
  dashboard development, regression modeling, figure generation

---

## Tools Used

Python: pandas, numpy, pyarrow, geopandas, plotly, panel, statsmodels, scikit-learn,
scipy, requests, httpx, aiohttp, beautifulsoup4, python-dotenv

Reporting: Quarto (PDF/HTML), docx (Word), matplotlib/seaborn (static figures)

Reference management: Zotero (with Better BibTeX plugin for APA exports)

---

## Project Plan

The full project plan — including DPSIR framework analysis, 26 data sources with URLs,
API endpoint cheat sheet, 30-variable schema table, scenario parameters, and 6-week
timeline — is in `Iowa_DC_Impact_Project_Plan.docx` at the project root.
