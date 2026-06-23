# Literature Synthesis Prompts
## Summarize, compare, and synthesize sources for writing

Use these when you have gathered sources and need to turn them into
thesis-quality writing: background sections, literature reviews,
methodology justifications, and discussion paragraphs.

---

### PROMPT: synthesize-water-literature
```
USE WHEN: Have 5-10 sources on data center water use and need a cohesive lit review paragraph
PASTE INTO: Claude (Cowork)
```

I need to write a literature review paragraph (200-300 words) on the topic of
data center water consumption and groundwater impacts for my PhD thesis.

I have gathered the following sources (paste citations and key findings):
[PASTE SOURCE SUMMARIES HERE — from prompts in 02_search_strategy.md]

Write a synthesis paragraph that:
1. Opens with the scale of the problem (global/national context)
2. Narrows to regional/Midwest context
3. Identifies the key methodological approaches used in prior work
4. Notes what prior studies have found about WUE benchmarks and water stress
5. Identifies the gap: what has NOT been studied (Iowa-specific, multi-facility,
   groundwater-specific, or longitudinal analysis)
6. Closes with how my study fills that gap

Style requirements:
- Academic, third person, past tense for prior work
- In-text citations in APA format: (Author, Year)
- No bullet points — flowing prose only
- Avoid starting sentences with "I" or "This paper"
- Flag any claims that need a specific citation I haven't yet provided

At the end, list: any additional sources I should find to strengthen this paragraph.
```

---

### PROMPT: synthesize-energy-literature
```
USE WHEN: Writing the energy/grid section background
PASTE INTO: Claude (Cowork)
```

I need a literature synthesis paragraph on Iowa's electricity grid and its relationship
to data center energy demand for my PhD thesis.

Key themes to cover:
1. US data center electricity consumption trends (Lawrence Berkeley LBNL estimates)
2. Iowa's grid transition: wind buildout and declining carbon intensity
3. MidAmerican Energy's renewable commitments and implications for data center Scope 2
4. Grid capacity and transmission constraints from large load additions
5. The tension between corporate renewable energy claims (PPAs) and location-based impact

Sources I have:
[PASTE YOUR GATHERED SOURCES]

Write 2 synthesis paragraphs:
- Paragraph 1: National data center energy context and Iowa grid characteristics
- Paragraph 2: The renewable energy accounting debate (location-based vs. market-based)
  and why it matters for impact measurement

Then suggest: what does this literature say about the right methodology for
attributing grid carbon emissions to data centers in Iowa? Should I use
location-based (MROW average) or market-based (subtract corporate PPAs)?
Cite the relevant methodological debates.
```

---

### PROMPT: compare-methodologies
```
USE WHEN: Need to justify a methodological choice by comparing to prior work
PASTE INTO: Claude (Cowork)
```

I need to justify my choice of [METHODOLOGY — e.g., "multiple OLS regression with
county-level aggregation rather than a spatial econometric model"] for analyzing
Iowa data center environmental impacts.

Prior studies used the following approaches:
[PASTE 3-5 METHOD DESCRIPTIONS FROM LITERATURE]

Write a methodology justification paragraph (150-200 words) that:
1. Acknowledges the range of methods used in the literature
2. States my chosen method and its core strengths for this application
3. Addresses the most likely methodological criticism (e.g., OLS assumes linearity,
   ignores spatial autocorrelation)
4. Explains why the limitation is acceptable given my data constraints and research scope
5. Notes how future work (PhD dissertation) could use a more sophisticated approach

This will go in the Methods section under "Analytical Approach."
```

---

### PROMPT: write-background-section
```
USE WHEN: Writing the Iowa-specific background section of the thesis
PASTE INTO: Claude (Cowork — web search enabled)
```

Write a background section (400-500 words) titled "Iowa as a Data Center Hub:
Context and Environmental Significance" for my PhD research thesis.

The section should cover:
1. Iowa's emergence as a top-5 US data center market (when it started, what drove it)
2. Key operators and their Iowa presence (Google Altoona, Meta Waukee/Altoona, Microsoft,
   QTS/Linn County, other significant operators)
3. Iowa-specific advantages that attracted data centers:
   - Tax incentives (Iowa sales tax exemption on electricity, property tax abatements)
   - Land availability and cost
   - MidAmerican Energy renewable energy infrastructure
   - Geological stability
   - Cold climate (free cooling advantage for PUE)
4. Iowa's specific vulnerabilities that make environmental study important:
   - Agricultural water dependency (Iowa is a top corn/soy producer, irrigation demand)
   - Jordan Aquifer stress and recharge limitations
   - Rural groundwater dependency for municipalities and farms
   - Drought trends in the region
5. The policy context: Linn County zoning debate, tax incentive reform discussions,
   Iowa DNR water permitting capacity

Cite specific facts where possible. Flag any claims that need a citation with [CITE NEEDED].
Write in academic prose, no bullet points. This will precede the literature review.
```

---

### PROMPT: write-findings-paragraph
```
USE WHEN: Have quantitative results and need to write a findings paragraph
PASTE INTO: Claude (Cowork)
```

Write an academic findings paragraph for my Iowa data center impact study
based on the following quantitative results:

[PASTE YOUR RESULTS — e.g., regression output, scenario table values, county rankings]

The paragraph should:
1. State the main finding in the first sentence (headline number)
2. Break down by dimension: water, energy, land if applicable
3. Note geographic concentration (which counties)
4. State the scenario range (low-high bound)
5. Acknowledge the key limitation or uncertainty
6. Close with the policy implication in one sentence

Requirements:
- 200-250 words
- Report confidence intervals or ranges, not just point estimates
- Use hedged language appropriate for estimates: "is estimated to," "approximately,"
  "under baseline assumptions"
- Do NOT overstate certainty — note what is based on estimated vs. disclosed data
- Third person, past tense ("the analysis found," "results indicate")

After the paragraph, write a single "thesis statement" sentence (1-2 lines) that
captures the most important finding for the abstract or introduction.
```

---

### PROMPT: draft-abstract
```
USE WHEN: Ready to write the project abstract or thesis proposal summary
PASTE INTO: Claude (Cowork)
```

Write a 250-word abstract for my class project / PhD research precursor titled:
"Environmental Impacts of Data Center Growth in Iowa: A Multi-Dimensional Assessment
of Water, Energy, and Land Use"

The abstract should follow this structure:
1. BACKGROUND (2 sentences): Iowa's rapid data center growth and why it matters
2. RESEARCH GAP (1 sentence): What has not been studied
3. METHODS (3 sentences): Data sources, analytical approach, scenario modeling
4. RESULTS (3-4 sentences): Key quantitative findings (fill in from my results)
5. CONCLUSION (2 sentences): Implications for policy and future research

Known results to include:
[PASTE YOUR KEY FINDINGS HERE]

Use hedged academic language. Do not overstate conclusions.
The abstract should be suitable for:
- A course project submission
- A conference abstract submission (adjust tone if needed)
- A PhD research proposal (should convey rigor and novelty)

After the abstract, write a 5-keyword list.
```
