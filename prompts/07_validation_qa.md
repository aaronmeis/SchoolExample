# Validation & QA Prompts
## Cross-check numbers, audit methodology, stress-test findings

Use these before presenting results, submitting work, or expanding to PhD scope.
These prompts are intentionally skeptical — they look for problems.

---

### PROMPT: sanity-check-water-numbers
```
USE WHEN: You have an estimated water withdrawal figure and need to verify it's plausible
PASTE INTO: Claude (Cowork — web search enabled)
```

I have estimated that Iowa data centers withdraw approximately [X] million/billion gallons
of water per year. Please help me verify this is plausible by cross-checking against
multiple independent reference points.

Cross-checks to perform:
1. Company disclosures: Search for Google, Meta, and Microsoft Iowa-specific water disclosures.
   Their Iowa facilities represent what fraction of my estimated total?
2. Iowa DNR large water user permits: The top 50 industrial water users in Iowa are published
   annually. Where would my estimated total rank?
3. WUE sanity check: My estimate implies a blended WUE of [Y] L/kWh across all Iowa DCs.
   Is this consistent with published WUE ranges for the cooling types used in Iowa?
4. Comparison to other known industrial users: How does the estimated DC water use compare to
   a reference point like Tyson Foods Iowa operations, or a medium-sized Iowa municipality?
5. Per-MW check: My estimate implies [Z] gallons/MW/year. Published WUE ranges suggest
   [RANGE] gallons/MW/year for evaporative cooling. Are these consistent?

For each cross-check:
- State what the reference point suggests
- State whether my estimate is CONSISTENT, HIGH, or LOW relative to the reference
- Suggest an adjustment if substantially off

Conclude with a confidence assessment: is my estimate probably accurate within ±20%,
±50%, or is the uncertainty larger? What is the single biggest source of uncertainty?
```

---

### PROMPT: audit-data-sources
```
USE WHEN: Before final submission — verify every data point has a traceable source
PASTE INTO: Claude Code
```

Read data/processed/facility_year.parquet and perform a data provenance audit.

For each column in the DataFrame, determine:
1. SOURCE: which collector script or manual entry created this column
2. RAW_FILE: the raw data file in data/raw/ that it came from
3. COVERAGE: % of rows with non-null values
4. SYNTHETIC_FLAG: is this column from generate_facilities() synthetic data or real data?

Generate a provenance table with these columns:
column_name | source_type | raw_file | coverage_pct | is_synthetic | notes

Then flag:
- Any column used in the regression model that is > 20% synthetic
- Any column with coverage < 70% that we still use in calculations
- Any column where the source file is missing from data/raw/ (meaning we can't reproduce it)

Print a "Reproducibility Score": what % of the analytical dataset can be reproduced
from scratch by running the collector scripts?

Save the provenance table to data/exports/data_provenance_audit.csv
```

---

### PROMPT: stress-test-assumptions
```
USE WHEN: Need to identify which assumptions most affect your conclusions
PASTE INTO: Claude (Cowork)
```

I need to stress-test the key assumptions in my Iowa data center impact analysis.

My central finding is: [PASTE YOUR HEADLINE FINDING]

The key assumptions underlying this finding are:
1. WUE default for facilities without disclosed data: [X] L/kWh
2. IT capacity utilization factor: [Y]% (I assume 85%)
3. Facilities included: I only count [operating | operating + under construction | all]
4. Aquifer attribution: I assume all Iowa DC water comes from groundwater (vs. surface water)
5. Grid carbon factor: I use MROW average ([C] lbs/MWh) not facility-specific

For each assumption:
a. State the impact if I change it to a reasonable alternative value
b. Calculate what my headline finding becomes under that alternative
c. Assess whether the alternative value is more or less defensible than my choice
d. Recommend: keep my assumption, change it, or add it as a sensitivity scenario

Format: a table with columns: Assumption | Base Value | Alternative | Finding Change | Defensibility | Recommendation

Then: which 2 assumptions are my results most sensitive to? These should be explicitly
discussed as limitations in my thesis.
```

---

### PROMPT: peer-review-simulation
```
USE WHEN: Want a critical review of a methods or findings section before submission
PASTE INTO: Claude (Cowork)
```

Act as a skeptical peer reviewer for a PhD-level environmental impact study.
Review the following methods or findings text and identify weaknesses.

[PASTE YOUR METHODS OR FINDINGS TEXT]

As a reviewer, focus on:
1. CAUSALITY: Am I conflating correlation with causation anywhere?
2. SELECTION BIAS: Am I only using facilities with disclosed data, creating bias toward
   large hyperscalers and underrepresenting smaller colos?
3. COUNTERFACTUAL: What would have happened without data centers? (displaced vs. new water use)
4. TEMPORAL: Am I treating a cross-sectional dataset as if it shows temporal causation?
5. SPATIAL: Am I attributing county-level impacts to specific aquifers without proof?
6. DATA QUALITY: Which claims rest on estimated/synthetic data vs. verified disclosures?
7. MISSING VARIABLES: What important confounders am I not controlling for?
8. GENERALIZABILITY: Can my Iowa findings generalize? Am I overclaiming?

For each issue found:
- Quote the specific text that has the problem
- Explain the methodological concern
- Suggest a specific fix or hedge

Also note: what are the 2-3 genuine strengths of this analysis I should highlight?
This simulation will help me preempt reviewer critiques before submission.
```

---

### PROMPT: citation-check
```
USE WHEN: Verifying that all claims in a draft are properly supported
PASTE INTO: Claude (Cowork)
```

Review the following draft text and identify every factual claim that needs a citation.

[PASTE YOUR DRAFT TEXT]

For each claim:
1. Quote the specific claim
2. Classify as: STATISTICAL FACT | METHODOLOGICAL CLAIM | HISTORICAL FACT | PROJECTION
3. Assess whether it is: WELL-SUPPORTED (citation in my source list) | NEEDS CITATION | COMMON KNOWLEDGE (no cite needed)
4. For NEEDS CITATION: suggest the most likely source (author, org, or dataset)
5. Flag any claim that may be outdated (could have changed since 2023)

My available sources:
[PASTE YOUR ZOTERO LIBRARY EXPORT OR LIST OF CITATIONS]

After the review, identify:
- The 3 most important missing citations (highest-stakes unsupported claims)
- Any claims I should soften with hedging language even if I have a source
  (e.g., estimated figures that could have significant error bars)
```

---

### PROMPT: units-consistency-check
```
USE WHEN: Before finalizing any table or figure — catch unit errors
PASTE INTO: Claude (Cowork)
```

Perform a units consistency audit on the following data table or results section.

[PASTE TABLE OR RESULTS TEXT]

Check for:
1. MIXED UNITS: Are any rows or columns mixing gallons and liters? MWh and TWh? kW and MW?
2. SCALE ERRORS: Does the order of magnitude make sense?
   (e.g., a single data center using 1 billion gallons/day would be flagged as likely wrong)
3. IMPLICIT UNITS: Any column header or value where the unit is ambiguous or unstated?
4. RATE vs. STOCK: Any confusion between annual flow (gallons/year) and cumulative total?
5. NORMALIZATION CONSISTENCY: If some values are per-MW and others are totals, flag it

For each issue: quote the problem, state the likely intended unit, and suggest the correction.

Then verify these specific conversions appear correct in context:
- 1 liter = 0.264172 gallons
- 1 m³ = 264.172 gallons = 1000 liters
- 1 TWh = 1,000,000 MWh = 1,000,000,000 kWh
- 1 metric ton CO2e = 2,204.62 lbs CO2e
- 1 MW × 8,760 hours × 85% CF = 7,446 MWh/year IT energy
```

---

### PROMPT: final-checklist
```
USE WHEN: Final review before class submission or thesis proposal submission
PASTE INTO: Claude (Cowork)
```

Act as my academic advisor reviewing my Iowa data center environmental impact
class project / PhD precursor before submission.

I will attach or paste my final report. Review it against this checklist:

RESEARCH DESIGN
[ ] Clear, testable research questions stated upfront
[ ] DPSIR or equivalent framework explicitly applied
[ ] Scope limitations clearly stated (what is NOT covered and why)

DATA
[ ] All data sources cited with URLs and access dates
[ ] Distinctions between disclosed vs. estimated values are clear
[ ] Missing data handling documented (imputation method or exclusion rule)
[ ] Sample size (N facilities, N counties, N years) stated

ANALYSIS
[ ] All formulas (PUE, WUE, CUE) defined on first use with units
[ ] Regression model specified completely (all predictors, reference categories)
[ ] Scenario assumptions stated numerically (not just "high growth scenario")
[ ] Uncertainty ranges provided for key projections (not just point estimates)

FINDINGS
[ ] Headline findings are quantified with appropriate precision (not over-precise)
[ ] Comparisons made to external benchmarks or reference points
[ ] Geographic specificity: county-level where data allows, not just "Iowa"

LIMITATIONS
[ ] Top 3 limitations explicitly stated
[ ] Known data gaps identified
[ ] Suggestions for how future work could address them

REPRODUCIBILITY
[ ] Code repository referenced (GitHub URL)
[ ] Data sources sufficient for another researcher to replicate

For each item: PASS | FAIL | PARTIAL — with a one-line note.
Priority fixes: list the top 3 items to address before submission.
```
