---
name: clinical-trial-matching
description: "自动整合 ClinicalTrials.gov + ChiCTR 临床试验数据，根据患者的癌症类型、基因突变和治疗史筛选正在招募的试验，给出入组医院地址、联系电话和入组要求。Triggers on: 临床试验, trial matching, 匹配试验, 找临床试验, 临床入组, NCT, ChiCTR"
license: MIT
metadata:
  author: CancerDAO
  version: "1.0.0"
  inspired_by: NCBI TrialGPT
  generalization_principle: "Code is mechanism. Knowledge is in subskills."
---

# Clinical Trial Matching

End-to-end oncology clinical trial matching that generates decision-grade HTML reports for patients (particularly Chinese patients) by coordinating dual-source retrieval (ClinicalTrials.gov + ChiCTR) with LLM analysis.

> This tool provides information matching only. It does not constitute medical advice or treatment recommendations. All enrollment decisions must be reviewed by a qualified clinical research team.

## When to use

Invoke this skill when the user mentions:

- "找临床试验", "临床入组", "匹配试验"
- "trial matching", "clinical trial", " NCT ", "ChiCTR"
- "临床试验匹配", "试验筛选"
- Providing a cancer diagnosis with molecular profile and requesting trial options

## Workflow

### Step 1 — Patient profile extraction

Normalize patient information into a structured profile. Required fields:

- `cancer_type`: CRC | NSCLC | PDAC | breast | gastric | etc.
- `stage`: IV | III | II | I
- `mutations`: Array of targetable mutations (e.g., `["KRAS G12C", "TP53"]`)
- `treatment_history`: Array of prior regimens with outcomes (PR | SD | PD)
- `disease_stage`: metastatic | locally_advanced | resectable
- `ecog`: 0-4
- `willing_to_travel_domestic`: true | false

Optional but important: `prior_therapies`, `biomarkers_known`, `organ_function`, `affordability_tier`

### Step 2 — Generate search plan

Create an 8-dimension search plan:

1. **Disease + mutation specific** — e.g. `NSCLC EGFR T790M`
2. **Generalized solid tumor** — e.g. `solid tumor KRAS G12C` (basket trials)
3. **Combination targets** — e.g. `KRAS G12C cetuximab`
4. **Pathway / resistance strategy** — e.g. `RAS-ON inhibitor`, `pan-KRAS`
5. **Exhaustive drug names** — all investigational + approved drugs in target class
6. **Cell therapy** — CAR-T, TIL, TCR-T for cancer type + targets
7. **Immunotherapy (post-PD-1)** — bispecific, LAG-3, TIGIT for resistance
8. **Chinese keywords (ChiCTR)** — single-token queries in Chinese

### Step 3 — Dual-source retrieval

Use two parallel sources:


**ClinicalTrials.gov** — via `mcp__oncology_db__search_trials` or direct API:
```python
mcp__oncology_db__search_trials(query="KRAS G12C NSCLC", status="RECRUITING")
```

**ChiCTR** — via ChiCTR MCP:
```python
mcp__chictr__search_trials(keyword="结直肠癌 KRAS G12C", max_results=20)
mcp__chictr__search_trials(keyword="胰腺癌 BRCA", max_results=20)
```

Merge results by registration number into unified `trial_results.json`.

### Step 4 — Verification + feasibility scoring

For each trial candidate:
- Verify recruitment status via live API
- Score on 5 dimensions: recruiting status, geographic access, time cost, financial cost, slot availability
- Filter to top candidates (feasibility threshold configurable)

### Step 5 — Per-trial LLM analysis

For each verified trial candidate, evaluate:

1. **Eligibility gating** — criterion-by-criterion match (prior therapy, mutation status, disease stage)
2. **Risk annotation** — mechanism-specific risks for this patient
3. **Efficacy contextualization** — trial efficacy + comparison to standard of care

### Step 6 — Decision synthesis

Generate:
- Top-N matching trial paths (ranked by feasibility + diversity, not by "recommendation")
- Match rationale per trial (no numerical scores)
- Goals-of-Care trigger if patient is 3rd+ line or predicted OS below threshold
- Self-contact information (investigator/center info for patient to inquire directly)

### Step 7 — HTML report

Render a self-contained HTML report (no external assets) to `~/Downloads/临床试验匹配报告_{patient_id}_{date}.html`.

## Data sources

| Source | Tool | Coverage |
|--------|------|----------|
| ClinicalTrials.gov | `mcp__oncology_db__search_trials` | Global trials, NCT IDs |
| ChiCTR | `mcp__chictr__search_trials` | Chinese trials, ChiCTR IDs |

## Compliance

- **No numerical scores** in patient-facing output
- **No "推荐" / "recommend"** — use "匹配理由" / "match rationale"
- **No priority ranking stars** — top paths shown by feasibility + diversity
- **Disclaimer required** in every report footer
- Provide contact info for self-inquiry only, not directed referrals

## Safety disclaimer

Clinical trial matching is for informational purposes only. The generated report does not constitute medical advice. All enrollment decisions must be reviewed by a qualified clinical research team at the respective trial site. Patient eligibility determination is the responsibility of the trial investigator.

## File map

```
clinical-trial-matching/
├── SKILL.md                    # this file
├── evals/
│   ├── evals.json             # eval cases
│   └── files/                 # patient profiles
│       ├── patient1.json      # NSCLC EGFR, post-osimertinib
│       └── patient2.json      # CRC KRAS G12C, 2L progressed
└── README.md                  # setup + usage
```


## References

- TrialGPT (NCBI): https://github.com/ncbi-nlp/TrialGPT
- ChiCTR MCP server: https://github.com/PancrePal-xiaoyibao/chictr-mcp-server