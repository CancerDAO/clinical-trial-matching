---
name: cancerdao-clinical-trial-matching
description: "当癌症患者（特别是中国患者）需要匹配临床试验时使用——输入癌症类型、基因突变、治疗史，系统自动从 ClinicalTrials.gov + ChiCTR 双源检索正在招募的试验，输出符合入排标准的试验列表（含医院地址、联系电话、入组要求）。触发词：临床试验、匹配试验、找临床试验、临床入组、NCT、ChiCTR、临床试验匹配。禁止在没有真实 NCT/ChiCTR ID 时编造试验编号。"
license: MIT
metadata:
  author: CancerDAO
  version: "2.0.0"
  tags: oncology clinical-trials cancer egfr kras
  inspired_by: NCBI TrialGPT
---

# CancerDAO Clinical Trial Matching

End-to-end oncology clinical trial matching that generates decision-grade reports for patients by coordinating dual-source retrieval (ClinicalTrials.gov + ChiCTR) with structured eligibility analysis.

> ⚠️ **免责声明**：本工具仅提供信息匹配服务，不构成医疗建议。所有入组决策须由各试验机构临床研究团队审核。

## When to use

触发以下任一场景时激活本技能：

- "找临床试验"、"临床入组"、"匹配试验"
- "trial matching"、"clinical trial"、"NCT"、"ChiCTR"
- 提供癌症诊断 + 基因突变 + 治疗史，请求试验选项
- 患者说"耐药了想找试验"、"靶向药失效了怎么办"

**禁止**：在无法验证真实招募状态时编造 NCT/ChiCTR 编号。

## Workflow

### Step 1 — 患者信息提取

从用户输入中提取结构化患者档案，必填字段：

```json
{
  "cancer_type": "NSCLC | CRC | PDAC | breast | gastric | ...",
  "stage": "IV | III | II | I",
  "mutations": ["EGFR L858R", "KRAS G12C", ...],
  "treatment_history": [{"line": 1, "regimen": "...", "outcome": "PR|SD|PD"}, ...],
  "prior_therapies": ["奥希替尼", "帕博利珠单抗", ...],
  "ecog": "0-4"
}
```

参考：`references/oncology-db-mcp-api.md` 查看可用基因/靶点覆盖范围。

### Step 2 — 生成 8 维搜索计划

创建 8 个搜索维度（参考：`references/trialgpt-methodology.md`）：

1. **疾病+突变特异** — e.g. `NSCLC EGFR T790M`
2. **泛实体瘤** — e.g. `solid tumor KRAS G12C` (basket trials)
3. **联合靶点** — e.g. `KRAS G12C cetuximab`
4. **通路/耐药策略** — e.g. `RAS-ON inhibitor`, `pan-KRAS`
5. **exhaustive 药物名称** — 所有研究中的 + 已批准的同靶点药物
6. **细胞治疗** — CAR-T, TIL, TCR-T
7. **免疫（后PD-1）** — bispecific, LAG-3, TIGIT
8. **中文关键词（ChiCTR）** — 单 token 查询（ChiCTR 分词器较脆弱）

### Step 3 — 双源检索

**ClinicalTrials.gov** — 通过 `mcp__oncology_db__search_trials`：
```python
mcp__oncology_db__search_trials(query="KRAS G12C NSCLC", status="RECRUITING")
```

**ChiCTR** — 通过 `mcp__chictr__search_trials`：
```python
mcp__chictr__search_trials(keyword="结直肠癌 KRAS G12C", max_results=20)
```

详见：`references/chictr-search-guide.md`

### Step 4 — 验证招募状态 + 可行性评分

对每个候选试验：
- 验证 NCT ID 真实性（绝不虚构）
- 5 维可行性评分：招募状态、地理可及性、时间成本、经济成本、槽位可用性
- 过滤至 top candidates

### Step 5 — 逐试验 eligibility 分析

对每个候选试验评估：
1. **入排标准匹配** — 逐条 vs 患者档案
2. **风险注释** — 机制 × 癌种 × 患者风险（参考：`references/egfr-tki-resistance.md`、`references/kras-g12c-landscape.md`）
3. **疗效上下文** — 试验疗效 + vs 标准治疗

### Step 6 — 决策合成

输出：
- Top-N 匹配路径（按可行性 + 多样性，不按"推荐"排名）
- 每试验"匹配理由"（禁止数值评分）
- 自助联系信息（研究者/中心信息，供患者自行联系）
- Goals-of-Care 触发（3rd+ 线或预测 OS 低于阈值时）

### Step 7 — HTML 报告

生成自包含 HTML 报告至 `~/Downloads/临床试验匹配报告_{date}.html`（无外部资源）。

## 合规规则

- **禁止数值评分**展示给患者
- **禁止"推荐"**词汇 — 用"匹配理由"
- **禁止虚构试验 ID** — 只输出通过 live API 验证的真实 ID
- 每份报告必须包含免责声明

## 数据来源

| 来源 | 工具 | 覆盖 |
|---|---|---|
| ClinicalTrials.gov | `mcp__oncology_db__search_trials` | 全球试验，NCT IDs |
| ChiCTR | `mcp__chictr__search_trials` | 中国试验，ChiCTR IDs |

## 常见错误

| 错误 | 正确做法 |
|---|---|
| 编造 NCT ID | 只通过 live API 验证后输出真实 ID |
| 用"推荐"排名 | 用"匹配理由" + 可行性评分描述 |
| 忽略耐药机制 | 查阅 `references/egfr-tki-resistance.md` 或 `references/kras-g12c-landscape.md` |
| ChiCTR 复合查询 | 用单 token 分别查询，再合并（分词器脆弱） |

## File map

```
cancerdao-clinical-trial-matching/
├── SKILL.md
├── README.md
├── evals/
│   ├── evals.json
│   └── files/
│       ├── patient1.json
│       └── patient2.json
└── references/
    ├── trialgpt-methodology.md
    ├── chictr-search-guide.md
    ├── oncology-db-mcp-api.md
    ├── egfr-tki-resistance.md
    └── kras-g12c-landscape.md
```

## References

- `references/trialgpt-methodology.md` — 8 维搜索策略 + BM25+MedCPT 检索
- `references/chictr-search-guide.md` — ChiCTR MCP 使用 + 注册号格式
- `references/oncology-db-mcp-api.md` — oncology-db MCP 工具覆盖范围
- `references/egfr-tki-resistance.md` — EGFR TKI 耐药机制（C797S/MET amp 等）
- `references/kras-g12c-landscape.md` — KRAS G12C 试验 landscape
