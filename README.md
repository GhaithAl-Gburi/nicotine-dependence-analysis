# README — Motives for nicotine dependance and cessation among Iraqi college students (R)

**Repository authors:** Ghaith Al‑Gburi, Mustafa Al-Gburi

**Status:** _planned / not yet conducted._ The repository contains the analysis pipeline and results from testing that pipeline on simulated data.

## Quick view
Click to view the full results of analyzing simulated data:
 [Open simulated-data summary — rendered HTML (githack)](https://raw.githack.com/GhaithAl-Gburi/nicotine-dependence-analysis/main/Simulation%20results/simulated_data_summary.html)

---

## Purpose
This repository contains the R analysis code and supporting outputs prepared for a planned study examining the motives for nicotine dependence and cessation among undergraduate students in Iraq. Because the empirical study has not yet been conducted, the repository includes a set of simulation outputs used to validate scoring, psychometric checks, and modelling code.

The `Simulation results/` directory contains the outputs produced when the analysis pipeline was run on synthetic (simulated) datasets — including rendered HTML reports and auxiliary files (for example, item–item correlation matrices generated from data simulation).

> **Data privacy:** this repository does **not** include participant‑level identifiable data.

---

## Files in this repo
- `main_analysis.Rmd` — the primary R Markdown analysis notebook.
- `LICENSE` — AGPL-3.0 license.
- `README.md` — this file.
- `Simulation results/` — a directory containing rendered HTML reports and simulation outputs created while testing the analysis workflow on simulated data.
  - `data_simulation.Rmd` — the R Markdown analysis notebook used for generating simulated data.
  - `data_simulation.html` — a rendered HTML report documenting the data-generation process.
  - `sim_input.csv` — the simulated dataset used for testing the analytic pipeline.
  - `simulated_data_summary.html` — the main rendered analysis summary for the simulated dataset.
  - `item-item correlation/` — a directory that stores item–item correlation outputs created from the simulated RFQ and WISDM-37 responses

---

## Required R version & packages
- R (>= 4.0 recommended) 
- Required packages (used in this script):  
  - `dplyr` (data manipulation)  
  - `ggplot2` (visualization)  
  - `writexl` (`write_xlsx()` export)  
  - `lavaan` (confirmatory factor analysis: `cfa()`, `modificationIndices()`, `summary()`)  
  - `semTools` (composite reliability: `compRelSEM()` and related helpers)  
  - `robustbase` (robust regression: `lmrob()`)  
  - `car` (regression diagnostics / utility functions)  
  - `nnet` (multinomial regression: `multinom()`)  
  - `ppcor` (partial Spearman correlations: `pcor()`)
  - `MASS` (multivariate normal draws via `mvrnorm()`) 

Install packages in R with:

```r
install.packages(c(
  "dplyr", "ggplot2", "writexl",
  "lavaan", "semTools", "robustbase",
  "car", "nnet", "ppcor", "MASS"
))
```

---

## What the script does (high-level)

- Clears the R environment and sets a reproducible seed (`rm(list = ls()); set.seed(2000)`).
- Loads required packages: `dplyr`, `ggplot2`, `writexl`, `lavaan`, `semTools`, `robustbase`, `car`, `nnet`, `ppcor`.

- Reads `input.csv` into `data` (`data <- as.data.frame(read.csv("input.csv"))`).

- Recode & clean:
  - Converts coded dichotomous columns to labelled factors (`0/1` → `No`/`Yes`).
  - Normalizes multi-coded entries (e.g. `"0_1"`, `"0_2"`, `"1_2"`, `"2_2"`).
  - Converts behavioral rating columns to factors for summarization, later back to numeric for analysis.

- Sample filtering & exclusions (prints counts via `cat()` at each step):
  - Remove duplicate `deviceid` responses.
  - Keep undergraduates only (`undergrad_now == "Yes"`).
  - Exclude non-smokers (keep `smoker` / `ex_smoker`).
  - Apply attention checks (`attention_check_a`, `attention_check_b`) and retain attentive participants.
  - Compute per-row `response_threshold` and exclude responses with `response_time` ≤ threshold.

- Summaries:
  - `summarize_variable()` returns:
    - numeric → `Median: x (Q1: y, Q3: z)`.
    - factor → counts and percentages per level.
  - Builds `summary_df` listing each characteristic and its distribution.

- Item–item correlations (exported):
  - Compute Spearman item–item correlation matrices for WISDM-37 and RFQ item sets.
  - Write matrices to Excel (`write_xlsx()`):  
    - `WISDM-37 item-item correlation matrix.xlsx`  
    - `RFQ item-item correlation matrix.xlsx`

- Confirmatory factor analysis (CFA) — WISDM-37:
  - Fit models on WISDM items as **continuous** (`estimator = "MLR"`) and **ordinal** (`estimator = "WLSMV", ordered = items`):
    - Model 1: 1-factor (unidimensional)
    - Model 2: 11-factor (multidimensional)
    - Model 3: 2-factor (PDM vs SDM)
    - Model 4: 2-order (first-order → second-order factors)
  - Print `summary()` (standardized estimates + fit measures).
  - Compare fits with `anova()` (chi-square diff tests).
  - Placeholders (`eval = FALSE`) for: model selection, `modificationIndices()`, respecification, and `compRelSEM()` composite reliability.

- Score construction & derived variables:
  - Compute Fagerström-like totals: `cigg_f`, `ecigg_f`, `h_f` (row sums).
  - Compute WISDM-37 factor means (`auto`, `loc`, `tol`, `craving`, `taste`, `cog_e`, `w_control`, `cue`, `affect`, `attach`, `social`) and composites `pdm`, `sdm` (row means).
  - Append derived scores to `data_cleaned`.

- Robust multiple regression (WISDM → dependence):
  - Use `lmrob()` to predict dependence outcomes (`cigg_f`, `ecigg_f`, `h_f`) from WISDM factor means.
  - Print model summaries.
  - Compute Spearman correlation and partial Spearman correlation estimates; square and multiply by 100 to express percent variance contributions.

- Confirmatory factor analysis (CFA) — RFQ:
  - Mirror WISDM CFA workflow for RFQ items (continuous `MLR` and ordinal `WLSMV`), testing:
    - 1-factor, 4-factor, 2-factor, and 2-order models.
  - Print summaries and compare with `anova()`.
  - Placeholders for model selection, modification indices, respecification, and composite reliability.

- RFQ scoring & outcome modelling:
  - Compute RFQ factor means (`hc`, `sc`, `ir`, `sp`) and higher-order composites (`intrinsic`, `extrinsic`).
  - Fit multinomial regression (`multinom()`) predicting `time_since_quitting` from RFQ factors; extract z-statistics and two-tailed p-values.

- Outputs & side effects:
  - Writes correlation matrices to Excel, prints summaries/CFA/regression outputs to console, and appends derived score columns to `data_cleaned`.
  - Several `eval = FALSE` sections remain as placeholders for final model selection, modification-index guided re-specification, composite reliability, and corrected item–total calculations.


---

## How to run

The analysis is written as an **R Markdown** document. You can run it either by *rendering* the Rmd to evaluated chunks and produces an HTML/PDF/MD output or by *extracting* the R code to a plain `.R` script and executing that.

From R (repository root)
```r
# 1) Render the R Markdown (recommended)
# Make sure the rmarkdown package is installed: install.packages("rmarkdown")
rmarkdown::render("main_analysis.Rmd")        # produces HTML (or other, per YAML)

# 2) Extract R code from the Rmd and run the .R script
# (knitr::purl will create main_analysis.R containing the R chunks)
knitr::purl("main_analysis.Rmd", output = "main_analysis.R")
source("main_analysis.R")                     # run inside current R session
```

---

## Reproducibility & recommended tweaks
- The script sets `set.seed(2000)` — keep this (or set your own fixed integer) to make the simulated data, `mvrnorm()` draws, `sample()` results and any bootstrap / random-start procedures reproducible across runs.  
- If you plan to run the Rmd unattended (CI, remote server, or batch): create output folders and explicitly persist everything you care about (figures, tables, model objects, human-readable summaries). Example additions you can paste into the Rmd after the relevant objects are created:

```r
# create output folders (idempotent)
dir.create("outputs", showWarnings = FALSE, recursive = TRUE)

# save summary tables / correlation matrices
writexl::write_xlsx(summary_df, "outputs/summary_df.xlsx")
write.csv(summary_df, "outputs/summary_df.csv", row.names = FALSE)
writexl::write_xlsx(wisdm37_cor, "outputs/wisdm37_item_cor.xlsx")
writexl::write_xlsx(rfq_cor, "outputs/rfq_item_cor.xlsx")

# save fitted models and readable summaries
saveRDS(cont_mod2_fit, "outputs/cont_mod2_fit.rds")
capture.output(summary(cont_mod2_fit, standardized = TRUE, fit.measures = TRUE),
               file = "outputs/cont_mod2_fit_summary.txt")

# save regression objects and CSV summaries
saveRDS(wisdm37_cigg, "outputs/wisdm37_cigg.rds")
# if you want a tidy table, use broom::tidy() then write.csv()

# export cleaned data including derived scores
write.csv(data_cleaned, "outputs/data_cleaned_with_scores.csv", row.names = FALSE)
```

---

## Notes about the study (short)


---

## License & citation
**License:** This repository is released under the **GNU Affero General Public License v3.0**.

**How to cite this code:**  

```

```

---

## Contact
- **Author:** Ghaith Al‑Gburi
- **Email:** ghaith.ali.khaleel@gmail.com 
- **GitHub:** `https://github.com/GhaithAl-Gburi`  
- **ORCID:** `0000-0001-7427-8310` 


---
