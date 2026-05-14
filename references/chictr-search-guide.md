# ChiCTR Search Guide — Chinese Clinical Trial Registry

## Overview

The Chinese Clinical Trial Registry (ChiCTR) is the primary clinical trial registry for the People's Republic of China, operated by the Chinese University of Hong Kong. It aligns with WHO ICMJE standards and is recognized by Chinese regulatory authorities.

**Website:** https://www.chictr.org.cn
**API Endpoint:** Via MCP tool `mcp__chictr__search_trials`

---

## MCP Tool Capabilities

### search_trials

```
mcp__chictr__search_trials({
  keyword?: string,          // Title keyword (e.g., "EGFR", "胰腺癌")
  registration_number?: string,  // Exact match, e.g., "ChiCTR2500111173"
  year?: number,            // Registration year, e.g., 2024, 2025
  max_results?: number      // Default 20, max results per query
})
```

**Returns:** Array of trial objects with:
- `registration_number` — ChiCTR registration ID
- `project_id` — Internal project ID
- `title` — Trial title in Chinese
- `study_type` — Study design type
- `registration_date` — Registration date (YYYY/MM/DD)
- `institution` — Recruiting institution

### get_trial_detail

```
mcp__chictr__get_trial_detail({
  registration_number: string  // e.g., "ChiCTR2600124574"
})
```

**Returns detailed fields:**
- `basic_info` — Full title (Chinese + English), registration status, dates
- `contact_info` — PI name, phone, email, institution
- `ethics_info` — Committee name, approval number, approval date
- `study_info` — Disease, study type, phase, design, objectives
- `sponsor_info` — Primary sponsor, funding source
- `recruitment_info` — Status, start/end dates
- `interventions` — Treatment groups with sample size
- `inclusion_criteria` — Full text (Chinese + English)
- `exclusion_criteria` — Full text (Chinese + English)

---

## ChiCTR Data Format

### Registration Number Format

- `ChiCTR2600124574` — Format: ChiCTR + YY + 10-digit sequence
- `ChiCTR` prefix + 2-digit year (26 = 2026) + 10-digit ID
- Prior to 2020: `ChiCTR-` prefix with different format (e.g., ChiCTR-ONRC-20001234)

### Study Types

| Chinese | English |
|---------|---------|
| 干预性研究 | Interventional study |
| 观察性研究 | Observational study |
| 病因学/横断研究 | Etiology/Cross-sectional |
| 诊断性试验 | Diagnostic test |
| 预后研究 | Prognostic study |

### Study Phases

- I / II / III / IV (standard)
- "其它" (N/A) for device or behavioral studies

### Registration Status Values

| Chinese | English |
|---------|---------|
| 预注册 | Prospective registration |
| 补注册 | Retrospective registration |
| 进行中 | Recruiting |
| 已完成 | Completed |
| 终止 | Terminated |

---

## Search Strategies for Our Skill

### 1. By Biomarker/Target

```javascript
// Example: EGFR mutations
const trials = await mcp__chictr__search_trials({
  keyword: "EGFR",
  max_results: 20
});
```

### 2. By Drug Name

```javascript
// Example: Osimertinib trials in China
const trials = await mcp__chictr__search_trials({
  keyword: "奥希替尼",
  max_results: 20
});
```

### 3. By Registration Number (Exact)

```javascript
// Example: Retrieve specific trial
const detail = await mcp__chictr__get_trial_detail({
  registration_number: "ChiCTR2600124574"
});
```

### 4. By Year

```javascript
// Example: All registered trials in 2025
const trials = await mcp__chictr__search_trials({
  year: 2025,
  max_results: 50
});
```

---

## Sample Trial Entry (ChiCTR2600124574)

```
Registration Number: ChiCTR2600124574
Title: 阿美替尼联合巩固性放疗或热消融治疗晚期EGFR突变非小细胞肺癌的安全性和疗效研究
Title (EN): A Study on the Safety and Efficacy of Aumolertinib Combined with 
             Consolidative Radiotherapy or Thermal Ablation in the Treatment of 
             Advanced EGFR-Mutated Non-Small Cell Lung Cancer
Study Type: 干预性研究 (Interventional)
Phase: 其它 (N/A)
Institution: 中山市小榄人民医院
Registration Date: 2026-05-14
Status: 尚未开始 (Not yet recruiting)
Sample Size: 40
Inclusion: EGFR敏感突变 (19外显子缺失或21外显子L858R突变), 未接受过EGFR-TKIs
```

---

## Data Quality Notes

- ChiCTR records include both Chinese and English title translations in `title_en` field
- Contact info often includes mobile numbers (useful for PI outreach)
- Inclusion/exclusion criteria are fully bilingual in `*_en` fields
- Some trials have incomplete English translations; Chinese text is always complete
- Ethics committee info and approval numbers are consistently recorded

---

## Integration with TrialGPT Pipeline

ChiCTR data can be used as supplementary trial sources alongside ClinicalTrials.gov for:

1. **Retrieval stage** — Generate Chinese-language keywords to match ChiCTR titles
2. **Matching stage** — Use bilingual inclusion/exclusion criteria for cross-registry matching
3. **Ranking stage** — Combine scores from multiple registries

**Note:** ChiCTR does not expose a public API; the MCP tool wraps a backend service. Rate limits and availability depend on service configuration.

---

## References

- ChiCTR Official: https://www.chictr.org.cn
- WHO Registry Network: https://www.who.int/clinical-trials-registry-platform
- Search accessed via `mcp__chictr__` MCP tool (oncology-db server)
- Research date: 2026-05-14