Here is a table detailing the specific prompt file names, the prompt identifiers, and what each prompt is designed to do based on the project documentation:

| File Name | Prompt Identifier | Description |
| ----- | ----- | ----- |
| `prompts/01_data_elicitation.md` | `extract-epa-egrid` | Extracts EPA eGRID data for subregion carbon intensity and fuel mix to Parquet. |
| `prompts/02_search_strategy.md` | `lit-search-academic` | Searches for academic papers on data center WUE and aquifer stress via Elicit. |
|  | `lit-search-grey` & `search-facility-news` | Crawls grey literature and news archives using the Tavily connector. |
| `prompts/03_collection_workflow.md` | `geocode-batch` | Batch geocodes Iowa addresses into spatial coordinates via Nominatim API. |
|  | `scrape-datacentermap` | Scrapes facility listings and operator specs from Data Center Map. |
| `prompts/04_impact_measurement.md` | `spatial-hotspot-analysis` | Calculates environmental burden scores by county for geospatial mapping. |
| `prompts/05_claude_code_iteration.md` | `add-real-data-swap` | Replaces synthetic generators with real data pipeline calls in the dashboard. |

