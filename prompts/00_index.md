# Prompt Library — Iowa Data Center Environmental Impact Research
## PhD Research Precursor | Python for Data Science

This folder contains copy-paste-ready prompts organized by research activity.
Use them with Claude (Cowork / Claude.ai), Claude Code (terminal), or any LLM.

---

## File Map

| File | Purpose | When to Use |
|---|---|---|
| `01_data_elicitation.md` | Extract structured data from PDFs, reports, web pages, and company disclosures | You have a document and need numbers out of it |
| `02_search_strategy.md` | Find academic literature, government reports, datasets, and news | Starting a new dimension or needing citations |
| `03_collection_workflow.md` | Systematic API pulling, scraping, and manual data entry workflows | Setting up or debugging a data collector |
| `04_impact_measurement.md` | Quantify and frame environmental impacts — DPSIR, LCA, scenario analysis | Analysis and writing phases |
| `05_claude_code_iteration.md` | Targeted prompts for Python coding, debugging, and dashboard iteration | Inside Claude Code terminal sessions |
| `06_literature_synthesis.md` | Summarize, compare, and synthesize research papers and reports | Writing background, lit review, methodology |
| `07_validation_qa.md` | Validate data, cross-check numbers, audit methodology | Before submitting or presenting results |

---

## How to Use

**In Claude (Cowork):** Paste the prompt block directly into the chat. Fill in `[BRACKETED]` fields with your specific values.

**In Claude Code:** Run `claude` from the `iowa-dc-impact/` directory, then paste the prompt. Claude Code has access to all project files so it can read, write, and run code directly.

**Chaining prompts:** The prompts are designed to chain — run a Search prompt first to find sources, then an Elicitation prompt to extract data, then a Collection prompt to structure it, then an Impact prompt to analyze it.

---

## Prompt Naming Convention

Each prompt block starts with a header line:
```
PROMPT: [short name]
USE WHEN: [trigger condition]
PASTE INTO: [Claude | Claude Code | both]
```

Fill `[BRACKETED]` placeholders before sending. Leave `{CURLY}` placeholders — those get filled by the model.
