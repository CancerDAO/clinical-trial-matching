# Clinical Trial Matching Skill

Oncology clinical trial matching skill that integrates ClinicalTrials.gov and ChiCTR data sources to identify recruiting trials for patients based on cancer type, mutations, and treatment history.

## Install

```bash
npx skills add CancerDAO/clinical-trial-matching
```

Or clone and reference locally:

```bash
git clone https://github.com/CancerDAO/clinical-trial-matching.git
```

## Usage

```
/clinical-trial-matching
```

Then describe the patient's condition, for example:


> 我父亲刚确诊肺腺癌四期，EGFR突变，用过奥希替尼，现在耐药了。想找临床试验。

## Data Sources

| Source | Description |
|--------|-------------|
| ClinicalTrials.gov | Global trials via NCT IDs |
| ChiCTR | Chinese clinical trials registry |

## Workflow

1. Extract patient profile from input
2. Generate 8-dimension search plan
3. Dual-source retrieval (ClinicalTrials.gov + ChiCTR)
4. Verify recruitment status + feasibility scoring
5. Per-trial LLM analysis (eligibility, risk, efficacy)
6. Decision synthesis (Top-N paths + Goals-of-Care)
7. Render self-contained HTML report

## Evals

Run eval cases:

```bash
# Eval 1: NSCLC EGFR post-osimertinib
/clinical-trial-matching
我父亲刚确诊肺腺癌四期，EGFR突变，用过奥希替尼，现在耐药了。想找临床试验。

# Eval 2: CRC KRAS G12C 2L progressed
/clinical-trial-matching
结肠癌，KRAS G12C突变，二线治疗后进展，想找临床试验

# Eval 3: ChiCTR pancreatic BRCA
/clinical-trial-matching
我想找ChiCTR注册的临床试验，胰腺癌，BRCA突变
```

## Output

A self-contained HTML report with:
- Matching trial IDs (NCT / ChiCTR)
- Eligibility criteria assessment
- Hospital locations + contact info
- Match rationale (no numerical scores)
- Medical disclaimer

Saved to `~/Downloads/临床试验匹配报告_{patient_id}_{date}.html`.


## Safety Disclaimer


This skill provides informational matching only. It does not constitute medical advice. All enrollment decisions must be reviewed by a qualified clinical research team at the respective trial site.