# Albuminuria Screening After a Laboratory Assay Change

**Temporal validation and SHAP explanation stability using NHANES**  
**Partha Pratim Ray** · Department of Computer Applications, Sikkim University, India

This repository contains an end-to-end notebook examining how a change in the measurement of urinary albumin affects the evaluation and interpretation of prediction models. The target is elevated urine albumin-to-creatinine ratio (UACR ≥30 mg/g) at one survey examination. The study endpoint is a single-examination laboratory outcome. Establishing chronic kidney disease requires evidence of chronicity. [KDIGO 2024 guideline](https://kdigo.org/wp-content/uploads/2024/03/KDIGO-2024-CKD-Guideline.pdf)

[View the executed notebook on GitHub](NHANES_Albuminuria_Assay_Shift_XAI_Colab.ipynb) · [Open in Google Colab](https://colab.research.google.com/github/ParthaPRay/Albuminuria-Screening/blob/main/NHANES_Albuminuria_Assay_Shift_XAI_Colab.ipynb)

## Study design

| Analysis | Comparison | Sample |
|---|---|---:|
| Frozen temporal evaluation | Train model pipelines and select cutoffs in the earlier cycle. Score the later cycle once per model and evaluate the same probabilities against both UACR label definitions. | 6,586 earlier development, 1,648 earlier grouped holdout, 5,870 later evaluation |
| Cross-cohort SHAP comparison | Compare cohort-level predictor importance from the same frozen XGBoost model using a common SHAP background. | 1,648 earlier holdout, 5,870 later evaluation |
| Paired XGBoost refits | Change the training labels while keeping the later-cycle training and holdout participants, preprocessing, hyperparameters, and SHAP background fixed. Compare predictions and explanations on the shared holdout. | 4,105 later training, 1,765 later holdout |

The earlier NHANES cycle used a fluorescent immunoassay for urinary albumin. The later cycle used liquid chromatography–tandem mass spectrometry. The notebook compares the released UACR with a recalculated UACR based on the NCHS backward Deming equation for expressing later urinary albumin on the earlier assay scale. The harmonized value uses the reported urinary creatinine. The notebook floors negative fitted albumin concentrations at zero before recalculating UACR. [Earlier laboratory documentation](https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_ALB_CR.htm) · [Later laboratory documentation and bridging equation](https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2021/DataFiles/ALB_CR_L.htm)

## What the notebook covers

1. **Data retrieval and cohort assembly.** Downloads public NHANES SAS transport files, records their source URLs and SHA-256 hashes, merges records by participant identifier, and tabulates eligibility and missingness.
2. **Assay harmonization.** Constructs reported and harmonized labels for the later cohort and shows which participants cross the 30 mg/g threshold.
3. **Model development.** Compares age-and-sex logistic regression, full-predictor logistic regression, and XGBoost. It fits imputation and applicable scaling within the training partitions and selects probability cutoffs from grouped out-of-fold predictions.
4. **Survey-weighted evaluation.** Reports discrimination, probability accuracy, calibration, and cutoff-specific metrics. Paired Fay balanced repeated replication provides confidence intervals. The primary comparison is the harmonized-minus-reported change in the frozen XGBoost Brier score.
5. **Sensitivity analyses and explanations.** Decomposes the Brier-score change by label transition, checks the primary standard error using Taylor linearization, examines participants with complete predictors and those outside the near-threshold band, and compares global and participant-level TreeSHAP attributions.

The notebook displays seven figures as 600 dpi PNGs with the underlying values printed below. Tables are tab-separated for direct copying.

## Results from the committed Colab run

| Result | Recorded value |
|---|---:|
| Later-cycle labels changed after harmonization | 78 of 5,870 (1.28% survey-weighted) |
| Survey-weighted prevalence under reported and harmonized labels | 10.39% and 11.50% |
| Frozen XGBoost AUROC under reported and harmonized labels | 0.75337 and 0.73887 |
| Frozen XGBoost Brier score under reported and harmonized labels | 0.0824155 and 0.0900496 |
| Primary paired XGBoost Brier-score change | +0.00763413 (95% CI 0.00481191 to 0.0104564) |
| Frozen-model global SHAP rank correlation across cohorts | 1.0000 |
| Global SHAP rank correlation between paired refits | 0.8833 |
| Agreement on the top-attributed predictor between refits | 75.64% after survey weighting |

The Brier-score contrast compares the **same probabilities** with two label definitions. The paired refits address a separate question about how training labels affect model explanations. The intervals describe survey sampling variability conditional on the fitted models and fixed NCHS bridging coefficients. The harmonized UACR is an estimated value on the earlier assay scale.

## Run in Google Colab

1. Open the notebook in Colab using the link above.
2. Select **Runtime → Change runtime type → T4 GPU**. A CPU runtime also works through the notebook's fallback path.
3. Select **Runtime → Run all**. The notebook downloads the source files directly from CDC and prints the input audit, package versions and results.

The committed run used a **Tesla T4**, **Python 3.13.16**, **scikit-learn 1.6.1**, **XGBoost 3.1.3**, and **SHAP 0.50.0**. The Colab setup cell installs the notebook's pinned XGBoost and SHAP versions. When the CUDA probe succeeds, XGBoost trains on the GPU. Data preparation, survey analysis, and fixed-background TreeSHAP run on the CPU.

## Data sources and attribution

- [NHANES 2017–March 2020 pre-pandemic public-use data](https://wwwn.cdc.gov/nchs/nhanes/search/DataPage.aspx?Cycle=2017-2020)
- [NHANES August 2021–August 2023 public-use data](https://wwwn.cdc.gov/nchs/nhanes/search/DataPage.aspx?Cycle=2021-2023)
- [NCHS NHANES survey methods and analytic guidelines](https://wwwn.cdc.gov/nchs/nhanes/analyticguidelines.aspx)

If you cite the notebook, credit **Partha Pratim Ray**, *Do albuminuria screening models remain reliable after a laboratory assay change?* [Jupyter notebook]. Cite the CDC/NCHS releases separately when using the underlying survey data. [GitHub repository](https://github.com/ParthaPRay/Albuminuria-Screening)
