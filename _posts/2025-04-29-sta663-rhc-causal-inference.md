---
title: "Estimating the Causal Effect of Right Heart Catheterization on 30-Day Mortality"
tags:
  - Causal Inference
  - Statistical Analysis
  - Python
  - Machine Learning
---

🚩 **Keywords: Propensity Score, IPW, Double Robust Estimation, Overlap Weighting, ATE** | [GitHub](https://github.com/zixiaotan21/STA663-Causual_Inference_Project)

This project was completed as the final project for the Spring 2025 section of **STA 663: Statistical Computing** at Duke University.

## Overview

Right Heart Catheterization (RHC) is an invasive diagnostic procedure widely used in ICUs — yet its causal effect on patient outcomes has long been debated. Using data from 5,735 critically ill hospitalized patients, this project estimates the **Average Treatment Effect (ATE)** of RHC on 30-day mortality using five different causal inference methodologies.

## Dataset

- **Source:** RHC observational study (`rhc_demo.csv`)
- **Sample size:** 5,735 adult patients
- **Treatment:** RHC performed within 24 hours of admission (n = 2,184 treated; n = 3,551 control)
- **Outcome:** Death within 30 days of hospital admission (binary)
- **Covariates:** 30+ variables including demographics, diagnoses (COPD, CHF, cancer), clinical markers (PaO₂/FiO₂, WBC, temperature), and functional status (DASI index)

## Methods

### 1. Missing Data Handling
Missing covariates were imputed using **MICE (Multiple Imputation by Chained Equations)** before any causal estimation.

### 2. Propensity Score Estimation
A logistic regression model using all 30+ covariates was fit to estimate P(Treatment = 1 | X). Overlap between treated and control propensity score distributions was verified visually.

### 3. Causal Estimators

| Method | Description |
|--------|-------------|
| **Outcome Regression** | Logistic regression models fit separately for treated/control; potential outcomes averaged |
| **IPW** | Inverse Probability Weighting using estimated propensity scores |
| **IPW Trimmed** | IPW with trimming at δ = 0.1 to reduce variance from extreme weights |
| **Overlap Weighting** | Weights proportional to probability of being in the opposite group — optimal for overlap region |
| **Double Robust (AIPW)** | Combines outcome model and propensity model; consistent if either model is correct |

### 4. Variance Estimation
Confidence intervals derived via **bootstrap resampling** and **sandwich estimators** for robustness.

## Results

| Method | ATE Estimate | 95% CI |
|--------|-------------|--------|
| Outcome Regression | 0.0451 | (0.0203, 0.0699) |
| IPW | 0.0425 | (0.0126, 0.0724) |
| IPW Trimmed | 0.0414 | (0.0125, 0.0703) |
| Overlap Weighting | 0.0440 | (0.0162, 0.0718) |
| **Double Robust** | **0.0403** | **(0.0133, 0.0672)** |

**Interpretation:** All five methods consistently estimate a positive ATE of ~0.040–0.045, suggesting RHC is associated with a 4–5 percentage point **increase** in 30-day mortality. The consistency across methods — each with different identifying assumptions — strengthens confidence in this finding.

## Balance Assessment

Standardized Mean Differences (SMDs) before and after weighting were computed for all covariates. Love plots confirm that IPW and overlap weighting achieve substantially improved covariate balance relative to the unadjusted comparison.

## Technical Stack

- **Language:** Python (Jupyter Notebook)
- **Packages:** `pandas`, `numpy`, `statsmodels`, `scikit-learn`, `scipy`, `fancyimpute`, `matplotlib`, `seaborn`

## Full Report

<embed src="/docus/sta663-rhc.pdf" width="100%" height="600" type="application/pdf">
