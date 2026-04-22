# README — Motives for Nicotine Dependence and Cessation Among Undergraduate College Students in Iraq (R)

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.17074154.svg)](https://doi.org/10.5281/zenodo.17074154)

**Repository authors:** Ghaith Al-Gburi, Mustafa Al-Gburi

**Study pre-registration:** The hypotheses, sampling plan, and analysis pipeline were registered on the Open Science Framework (OSF) prior to data collection.

[![View Pre-registration](https://img.shields.io/badge/OSF-Pre--registration-blue)](https://osf.io/5euwn/overview?view_only=51d12ebdc1cf45f79d1f42daaacbda27)

---

## Quick view
Click below to view the complete analysis results and output:

[Open the full analysis report (PDF)](https://rawcdn.githack.com/GhaithAl-Gburi/nicotine-dependence-analysis/main/analysis_report.pdf)

---

## Purpose
This repository contains `explore_nictotine_dependence.Rmd` — an R Markdown script used to examine the **motives for nicotine dependence and cessation among undergraduate college students in Iraq**. The script performs data cleaning and attention-based exclusion, generates sample characteristic tables, runs confirmatory factor analyses (CFA) for the **Brief Wisconsin Inventory of Smoking Dependence Motives (WISDM-37)** and the **Reasons For Quitting (RFQ)** scales, estimates internal consistency, and models the relationships between smoking motives and nicotine dependence (Fagerström Test for Nicotine Dependence, FTND) across traditional cigarettes, e-cigarettes, and hookah using MM-estimator robust regression. Relations between cessation motives and time since quitting are tested using Kruskal-Wallis tests with Benjamin-Hochberg correction.

> **Data privacy:** Participant-level identifiable data are **not included** in this repository.

---

## Files in this repo
- `explore_nictotine_dependence.Rmd` — R Markdown script used for data analysis.
- `nicotine_dataset.csv` — CSV file containing the data used for statistical analysis (Pending publication)
- `analysis_report.pdf` — Rendered PDF report for the complete analysis workflow.
- `Heatmaps.xlsx` — Excel file containing the item-item, item-factor, and factor-factor correlation matrices.
- `data collection tool/` — Directory containing the Arabic and English versions of the research survey.
- `Pre-registration testing/` — Directory containing scripts and rendered reports generated while testing the analysis workflow on simulated data (prior to data collection).
- `README.md` — This file.
- `LICENSE` — AGPL-3.0 License (reuse and citation conditions).

---

## Required R version & packages
- **R version:** ≥ 4.0 recommended
- **Required packages:** `dplyr`, `ggplot2`, `gtsummary`, `broom`, `writexl`, `lavaan`, `semTools`, `psych`, `robustbase`, `car`, `ppcor`, `rstatix`

---

## Expected data format (column names)

The script expects an R data frame (or CSV, loaded as `nicotine_dataset.csv`) with the following columns:

### Demographics and smoking status

| Group | Variables |
|-----------|------------|
| Identifiers | `ID`, `deviceid`, `response_time` |
| Demographics | `age`, `sex`, `ethnicity`, `marital_stat`, `residence`, `province` |
| Academic status | `undergrad_now`, `speciality`, `university_college` |
| Smoking status | `smoke_stat`, `smoke_now.cigarettes`, `smoke_now.e_cigarettes`, `smoke_now.hookah`, `smoke_past.cigarettes`, `smoke_past.e_cigarettes`, `smoke_past.hookah`, `smoke_most_current`, `smoke_most_ex`, `time_since_quitting` |
| Attention checks | `attention_check_a`, `attention_check_b` |

### Brief Wisconsin Inventory of Smoking Dependence Motives (WISDM-37)

| Subscale | Variables |
|-----------|------------|
| Automaticity | `auto1`–`auto4` |
| Loss of Control | `loc1`–`loc4` |
| Tolerance | `tol1`–`tol4` |
| Craving | `craving1`–`craving4` |
| Taste / Sensory Processes | `taste1`–`taste3` |
| Cognitive Enhancement | `cog_e1`–`cog_e3` |
| Weight Control | `w_control1`–`w_control3` |
| Cue Exposure / Associative Processes | `cue1`–`cue3` |
| Affective Enhancement | `affect1`–`affect3` |
| Attachment | `attach1`–`attach3` |
| Social / Environmental Goads | `social1`–`social3` |

### Reasons For Quitting (RFQ)

| Subscale | Variables |
|-----------|------------|
| Health Concerns | `hc1`–`hc5` |
| Self-Control | `sc1`–`sc5` |
| Immediate Reinforcement | `ir1`–`ir5` |
| Social Pressure | `sp1`–`sp5` |

### Fagerström Test for Nicotine Dependence (FTND)

| Smoking method | Variables |
|-----------|------------|
| Traditional cigarettes | `cigg_f1`–`cigg_f6` |
| E-cigarettes | `ecigg_f1`–`ecigg_f6` |
| Hookah | `h_f1`–`h_f6` |

---

## How to run

### Option 1 — Knit in RStudio
Open `explore_nictotine_dependence.Rmd` and click **Knit → PDF** (or HTML).

### Option 2 — Run from the console
```r
rmarkdown::render("explore_nictotine_dependence.Rmd")
```

### Option 3 — Command line
```bash
Rscript -e "rmarkdown::render('explore_nictotine_dependence.Rmd')"
```

---

## Outputs produced by the script

- **Sample flowchart:** Console counts at each exclusion step (duplicate `deviceid`, non-undergraduates, non-smokers, invalid age/university, participants outside Iraq, failed attention checks, implausibly fast response times).
- **Table 1 (`gtsummary`):** Sample characteristics stratified by `smoke_stat` (smoker vs. ex-smoker) with Wilcoxon rank-sum / Fisher's exact tests.
- **Geographic distribution:** Frequency and percentage of participants across Iraqi provinces.
- **Item-item Spearman correlation matrices** for WISDM-37 and RFQ items (exported to Excel).
- **Confirmatory Factor Analyses (CFA)** using `lavaan` with both `MLR` (continuous) and `WLSMV` (ordinal) estimators:
  - *WISDM-37:* 1-factor, 2-factor (PDM vs. SDM), 11-factor, and hierarchical (2nd-order) models.
  - *RFQ:* 1-factor, 2-factor (intrinsic vs. extrinsic), 4-factor, and hierarchical (2nd-order) models.
- **Model comparison:** Chi-square difference tests (`lavTestLRT`) and fit indices (CFI, TLI, RMSEA, SRMR).
- **Reliability analyses:** Corrected item-total Spearman correlations (`psych::alpha`) and composite reliability (`semTools::compRelSEM`).
- **Modification indices:** Sorted list of top suggestions for model refinement.
- **FTND score construction** and **waffle plots** (`ggplot2`) showing the distribution of dependence categories across traditional cigarettes, e-cigarettes, and hookah users.
- **MM-estimator robust regression** (`robustbase::lmrob`) modeling FTND scores on standardized WISDM-37 subscale scores, run separately for each smoking method and for a combined standardized dependence model.
- **Squared zero-order and partial Spearman correlations** expressing percent variance contributions of each WISDM-37 factor to nicotine dependence.
- **Kruskal-Wallis tests** (`rstatix`) with epsilon-squared effect sizes and Benjamini-Hochberg-adjusted p-values, comparing RFQ items across `time_since_quitting` groups.

---

## License & citation
**License:** This repository is released under the **AGPL-3.0 License**.

**How to cite this code:**

```
Al-Gburi, G., & Al-Gburi, M. (2025). nicotine-dependence-analysis. Zenodo. https://doi.org/10.5281/zenodo.17074154
```

---

## Contact
- **Author:** Ghaith Al-Gburi
- **Email:** ghaith.ali.khaleel@gmail.com
- **GitHub:** [https://github.com/GhaithAl-Gburi](https://github.com/GhaithAl-Gburi)
- **ORCID:** `0000-0001-7427-8310`

- **Co-author:** Mustafa Al-Gburi
- **Email:** mustafa.ali.gburi@gmail.com
- **GitHub:** [https://github.com/mustafagburi](https://github.com/mustafagburi)
- **ORCID:** `0009-0009-2699-8351`
