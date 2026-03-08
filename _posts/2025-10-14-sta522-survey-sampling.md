---
title: "U.S. College Characteristics: Stratified Random Sampling Analysis"
tags:
  - Survey Sampling
  - Statistical Analysis
  - Experimental Design
  - R
---

🚩 **Keywords: Stratified Sampling, Survey Design, Confidence Intervals, College Scorecard** | [GitHub](https://github.com/zixiaotan21/STA522-project1)

This project was completed with Zhihao Chen for the Fall 2025 section of **STA 522: Study Design and Causal Inference** at Duke University.

## Overview

Using publicly available data from the U.S. Department of Education's **College Scorecard**, we designed and implemented a stratified random sampling study to estimate key characteristics of U.S. degree-granting institutions — without surveying all 6,000+ schools in the population.

## Research Questions

1. What is the total undergraduate enrollment across all U.S. colleges?
2. What proportion of colleges offer an undergraduate Statistics major?
3. How do alumni earnings differ between public and private institutions?
4. How do annual attendance costs compare across institution types?

## Sampling Design

We applied **stratified random sampling with proportional allocation**, dividing the population into four strata based on two binary variables:

| Stratum | Control Type | Level |
|---------|-------------|-------|
| 1 | Public | 2-year |
| 2 | Public | 4-year |
| 3 | Private | 2-year |
| 4 | Private | 4-year |

- **Population:** All U.S. degree-granting institutions in the College Scorecard database
- **Sample size:** n = 100 institutions (proportionally allocated across strata)
- **Data source:** `Most-Recent-Cohorts-Institution` and `Most-Recent-Cohorts-Field-of-Study` files

## Estimation Methods

For each research question, we derived **design-based estimators** appropriate to the quantity of interest:

- **Total enrollment:** Horvitz-Thompson estimator for population totals
- **Proportion with Statistics major:** Ratio estimator with design-based variance
- **Earnings and cost comparisons:** Separate ratio estimators within stratum, combined using the stratified estimator formula
- **Confidence intervals:** 95% CIs constructed using the normal approximation with design-corrected standard errors

## Key Results

| Quantity | Estimate | 95% CI |
|----------|----------|--------|
| Total undergraduate enrollment | See report | See report |
| Proportion offering Stats major | See report | See report |
| Median alumni earnings — Public | See report | See report |
| Median alumni earnings — Private | See report | See report |
| Average annual cost — Public | See report | See report |
| Average annual cost — Private | See report | See report |

## Technical Stack

- **Language:** R
- **Packages:** `dplyr`, `ggplot2`, `tidyr`, `gridExtra`, `survey`

## Full Report

<embed src="/docus/sta522-project1.pdf" width="100%" height="600" type="application/pdf">
