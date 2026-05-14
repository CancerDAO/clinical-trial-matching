# EGFR TKI Resistance Mechanisms — Clinical Trial Matching Reference

## Overview

EGFR tyrosine kinase inhibitors (TKIs) are first-line therapy for EGFR-mutant NSCLC. Resistance develops through multiple mechanisms, which define distinct clinical trial enrollment categories.

**Primary 1st-gen TKI targets:** Gefitinib, Erlotinib
**Primary 2nd-gen TKI targets:** Afatinib, Dacomitinib
**Primary 3rd-gen TKI target:** Osimertinib (T790M-directed)

---

## Resistance Mechanisms by Line of Therapy

### 1. First/Second-Generation TKI Resistance → Osimertinib Era

#### T790M Mutation (Primary Resistance Mechanism)

- **Prevalence:** ~60% of cases after 1st/2nd-gen TKI progression
- **Mechanism:** Cysteine pocket mutation prevents drug binding
- **Clinical significance:** Defines transition to osimertinib
- **Evidence level:** A (FDA approved indication for osimertinib)

**Oncology-db data (EGFR T790M):**
- Sensitivity: Osimertinib (A), Rociletinib (B), combinations with pemetrexed/erlotinib
- Resistance: Erlotinib (A), Gefitinib (B), Afatinib (B), Dacomitinib (B)

---

### 2. Third-Generation TKI (Osimertinib) Resistance

After osimertinib, resistance mechanisms bifurcate into:

#### 2a. C797S Mutation

- **Type:** Cysteine → Serine at position 797
- **Prevalence:** ~15-20% of osimertinib-resistant cases
- **Mechanism:** Removes the covalent binding site for osimertinib
- **Context:** Usually occurs with retained T790M (T790M/C797S cis configuration)

**Oncology-db data (EGFR C797S):**

Sensitivity (support):
| Drug | Evidence Level | Combo Context |
|------|---------------|---------------|
| Brigatinib | D | Brigatinib + Cetuximab |
| Cetuximab | D | Brigatinib + Cetuximab |
| Panitumumab | D | Cetuximab + Brigatinib + Panitumumab |
| Afatinib | D | Monotherapy |
| Erlotinib + Osimertinib | D | Sequential combination |
| Osimertinib + Erlotinib | D | Sequential combination |

Resistance:
| Drug | Evidence Level |
|------|---------------|
| Osimertinib (monotherapy) | B |

**Clinical implication:** C797S is targetable with EGFR antibody combinations (brigatinib + anti-EGFR mAb). Clinical trials should include C797S stratification.

#### 2b. MET Amplification

- **Prevalence:** ~5-15% of osimertinib-resistant cases
- **Mechanism:** Bypass activation of downstream pathways (RAS-MAPK, PI3K-AKT)
- **Detection:** FISH or NGS copy number analysis
- **Oncology-db status:** No sensitivity/resistance drugs currently cataloged in get_target_treatments

**Potential targets:**
- Tepotinib (MET TKI) — FDA approved for MET exon 14 skipping
- Capmatinib (MET TKI) — FDA approved for MET exon 14 skipping
- These are approved for MET alterations, not specifically as osimertinib resistance combinations

**Clinical trial note:** MET amplification in osimertinib resistance typically investigated in combination trials with MET TKIs + osimertinib or newer EGFR inhibitors.

#### 2c. Other Bypass Resistance Mechanisms

| Mechanism | Prevalence | Key Targets |
|-----------|-----------|------------|
| HER2 amplification | ~2-5% | Trastuzumab, pertuzumab, T-DM1 |
| PIK3CA mutations | ~5-10% | Alpelisib, taselisib |
| BRAF mutations | ~1-3% | Dabrafenib + trametinib |
| SCLC transformation | ~5-10% | Platinum-etoposide chemotherapy |

#### 2d. EGFR-Dependent (On-target) Additional Mutations

| Mutation | Location | Clinical Relevance |
|----------|----------|-------------------|
| L718V/Q | ATP binding site | Reduces osimertinib binding |
| G724S | Exon 18 | Affects 3rd-gen TKI binding |
| V834I | Kinase domain | Rare resistance mechanism |
| E762G | Kinase domain | Rare resistance mechanism |

---

## EGFR Variant Landscape (from oncology-db)

### All EGFR Variants with Evidence Count > 10

| Variant | Evidence Count | Associated Drug Count |
|---------|--------------|---------------------|
| L858R | 72 | 21 |
| T790M | 60 | 22 |
| Exon 19 Deletion | 51 | 16 |
| Mutation (generic) | 27 | 11 |
| Amplification | 25 | 12 |
| E746_A750del | 22 | 5 |
| Overexpression | 16 | 10 |
| VIII (vIII) | 12 | 10 |
| S768I | 12 | 4 |
| G719A | 12 | 4 |
| G598V | 12 | 5 |
| Exon 20 Insertion | 12 | 9 |
| C797S | 12 | 6 |
| L747_P753delinsS | 11 | 3 |
| G719S | 11 | 4 |

---

## Osimertinib Label Information

**Mechanism (from FDA label):**
- Irreversible kinase inhibitor binding to T790M, L858R, exon 19 deletions
- Two active metabolites: AZ7550 (similar potency) and AZ5104 (8-fold more potent against exon 19 deletion/T790M)
- Also inhibits HER2, HER3, HER4, ACK1, BLK at clinically relevant concentrations
- Brain penetration (brain:plasma AUC ratio ~2)

**Indications:**
1. NSCLC with T790M EGFR mutation after 1st-gen EGFR TKI progression
2. First-line treatment of NSCLC with EGFR exon 19 deletions or L858R (independent of T790M status)
3. Unresectable or metastatic NSCLC with EGFR exon 19 deletions or L858R, with brain metastases

**Key Adverse Reactions:**
- Diarrhea (41%), musculoskeletal pain (25%), nail disorders (21%)
- QTc prolongation (4.3%)
- ILD/pneumonitis (3.4%)
- Hepatotoxicity (ALT/AST elevation in 38-39%)

---

## Clinical Trial Matching Implications

### Resistance-Driven Trial Enrollment Categories

1. **T790M-positive, C797S-negative** — Osimertinib or osimertinib combinations
2. **T790M-negative, C797S-positive** — Consider brigatinib + anti-EGFR antibody trials
3. **MET amplification** — MET TKI + EGFR TKI combinations or dedicated MET trials
4. **Multiple bypass mechanisms** — Basket trials for resistance signatures

### Key Biomarkers for Trial Matching

| Biomarker | Test Method | Trial Relevance |
|-----------|-------------|----------------|
| T790M | NGS, PCR, Cobas | Osimertinib eligibility |
| C797S | NGS | Next-gen EGFR TKI trials |
| MET amplification | FISH, NGS CNV | MET TKI combination trials |
| HER2 amplification | FISH, IHC | HER2-targeted trials |
| PIK3CA mutations | NGS | PI3K inhibitor trials |

---

## References

- FDA Drug Labels via oncology-db MCP (accessed 2026-05-14)
- Nature Communications: Jin et al. "Matching patients to clinical trials with LLMs" (2024)
- NCCN Guidelines for NSCLC (EGFR mutation section)
- oncology-db gene variant data (563 genes, 18,161 trials)