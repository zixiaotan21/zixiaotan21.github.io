---
title: "Street Pricing of Diazepam: Hierarchical Modeling of Geographic Heterogeneity"
tags:
  - Hierarchical Modeling
  - Bayesian Statistics
  - Statistical Analysis
  - R
---

🚩 **Keywords: Mixed Effects Model, BIC Model Selection, Bayesian vs. Frequentist, StreetRx, Geographic Variation** | [GitHub](https://github.com/zixiaotan21/STA610-Casestudy)

This project was completed for the Fall 2025 section of **STA 610: Multilevel and Hierarchical Models** at Duke University.

## Overview

Street drug pricing is shaped by a complex mix of pharmacological, economic, and geographic factors. Using crowdsourced data from the [StreetRx](https://streetrx.com/) platform, this study applies **hierarchical linear mixed models** to analyze what drives the price per milligram (ppm) of diazepam (a benzodiazepine) and whether significant pricing variation exists across U.S. states.

## Research Questions

1. Which variables are associated with the price per mg of diazepam on the street market?
2. Is there significant geographic heterogeneity in pricing across U.S. states?

## Data

- **Source:** StreetRx — a crowdsourced database of self-reported street drug transaction prices
- **Outcome:** Log-transformed price per milligram — log(ppm)
- **Key variables:** Dosage strength (mgstr), bulk purchase indicator, information source (word-of-mouth, internet, personal), state, year
- **Preprocessing:** Removed `primary_reason` (>50% missing); excluded erroneous/outlier entries

## Modeling Approach

### Model Structure

$$\log(\text{ppm}_{ij}) = \beta_0 + \beta_1 \cdot \text{mgstr}_{ij} + \beta_2 \cdot \text{bulk}_{ij} + \beta_3 \cdot \text{source}_{ij} + \beta_4 \cdot \text{year}_{ij} + u_j + \epsilon_{ij}$$

where $u_j \sim \mathcal{N}(0, \sigma_u^2)$ captures **state-level random intercepts** — the primary quantity of interest for geographic heterogeneity.

### Model Selection

An exhaustive search over **1,024 candidate models** (all subsets of fixed effects) was performed using **BIC** as the selection criterion. The optimal model includes dosage strength, bulk purchase, source, and year as fixed effects, with state random intercepts.

### Frequentist vs. Bayesian Comparison

Both approaches were implemented and compared:

| Framework | Method | Package |
|-----------|--------|---------|
| Frequentist | Restricted Maximum Likelihood (REML) | `lme4` |
| Bayesian | MCMC with weakly informative priors | `brms` / `rstan` |

Posterior distributions and REML estimates were nearly identical, providing strong cross-validation of the findings.

## Key Findings

| Factor | Effect on log(ppm) | Interpretation |
|--------|-------------------|----------------|
| Bulk purchase | **−0.124** | ~12.4% price reduction per mg for bulk transactions |
| Internet source | **−0.330** | ~33% lower prices vs. word-of-mouth |
| Personal report | **−0.103** | ~10% lower prices vs. word-of-mouth |
| State random effect (σ_u) | Significant | Substantial geographic variation in baseline pricing |

**Geographic heterogeneity:** The estimated between-state variance was statistically significant, confirming that state-level factors (supply chains, law enforcement, local demand) drive meaningful pricing differences. Texas was identified as a notable outlier with anomalous pricing patterns.

**Model convergence:** Frequentist and Bayesian parameter estimates converged nearly identically, confirming model robustness and that the data adequately inform the likelihood.

## Technical Stack

- **Language:** R
- **Packages:** `tidyverse`, `lme4`, `brms`, `rstan`, `kableExtra`, `patchwork`, `gridExtra`, `influence.ME`

## Full Report

<embed src="/docus/sta610-diazepam.pdf" width="100%" height="600" type="application/pdf">
