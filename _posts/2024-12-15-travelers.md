---
title: "Travelers University Modeling Competition: Policyholder Call Count Prediction"
tags:
  - Statistical Modeling
  - Machine Learning
  - Regression
  - Competition
---

In the fall of 2024, I participated in the **Travelers University Modeling Competition**, a data science competition hosted by Travelers Insurance Company at Duke University.

## Business Problem

The goal was to develop a predictive model to forecast **80,000 policyholder call counts** — the number of times a policyholder is likely to contact the call center — in order to optimize resource allocation and enhance cost-efficiency in call center operations.

## Modeling Challenge

The dataset presented a significant distributional challenge: **over 50% of call counts were zero**, making standard regression approaches unsuitable due to zero inflation.

To address this, I applied a **Zero-Inflated Poisson (ZIP) model**, which separates the zero-generating process from the count-generating process:

- A **Bernoulli distribution** classifies zeros as either *structural* (policyholders who will never call) or *random* (policyholders who happened not to call during the observation period)
- A **Poisson distribution** models the count of calls for the non-structural zero group

## Model Selection

I compared the ZIP model against gradient boosting methods including **XGBoost** and **LightGBM**. While boosting methods achieved competitive raw accuracy, the **Generalized Linear Model (GLM)** was ultimately selected for its:

- **Interpretability**: Coefficients have direct business meaning for the insurance context
- **Regulatory compliance**: Transparent models are preferred in the insurance industry
- **Performance**: Achieved a **Mean Squared Error (MSE) of 25.02** with the best **Akaike Information Criterion (AIC)** fit

## Key Results

| Model | MSE | AIC |
|---|---|---|
| ZIP / GLM | **25.02** | Best fit |
| XGBoost | Competitive | Less interpretable |
| LightGBM | Competitive | Less interpretable |

The final model provides actionable predictions that allow Travelers' call center operations team to proactively staff and allocate resources based on predicted policyholder contact behavior.
