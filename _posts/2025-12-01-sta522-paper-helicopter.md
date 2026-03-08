---
title: "Paper Helicopter Experiment: Full Factorial Design & Flight Optimization"
tags:
  - Experimental Design
  - Statistical Analysis
  - ANOVA
  - R
---

🚩 **Keywords: Full Factorial Design, ANOVA, Response Surface, Interaction Effects** | [GitHub](https://github.com/zixiaotan21/STA522-project2)

This project was completed with Zhihao Chen for the Fall 2025 section of **STA 522: Study Design and Causal Inference** at Duke University.

## Overview

Using a **2⁴ full factorial experiment**, we systematically investigated which physical properties of a paper helicopter most affect flight duration — and identified the optimal design configuration for maximum airtime.

## Experimental Design

We tested four binary factors across **16 treatment combinations**, each replicated **5 times** for a total of **80 flights**:

| Factor | Low Level | High Level |
|--------|-----------|------------|
| Rotor length | 7.5 cm | 8.5 cm |
| Leg length | 7.5 cm | 12.0 cm |
| Leg width | 3.2 cm | 5.0 cm |
| Paper clip | None | Attached |

- **Response variable:** Flight duration (seconds), measured from drop to landing
- **Randomization:** Flight order randomized within each replicate block

## Statistical Methods

- **Full factorial ANOVA** including all main effects and two-way/three-way interactions
- **Nested F-tests** for model comparison and interaction significance
- **Model diagnostics:** residual normality (Shapiro-Wilk), constant variance (Levene's test)
- **95% Confidence intervals** for individual treatment effects

## Key Findings

| Factor / Interaction | Effect on Flight Time | p-value |
|----------------------|-----------------------|---------|
| Rotor length (high) | **+0.220s** | < 0.001 |
| Paper clip | **−0.315s** | < 0.001 |
| Leg length (high) | −0.159s | < 0.01 |
| Leg width (high) | −0.152s | < 0.05 |
| Rotor × Leg length | Significant interaction | < 0.05 |
| Rotor × Clip | Significant interaction | < 0.05 |

**Interpretation:** Rotor length is the most important design factor — a longer rotor increases lift and slows descent. Paper clips add weight without aerodynamic benefit, sharply reducing flight time. The significant rotor × clip interaction suggests that clipping a long-rotor helicopter is especially detrimental.

## Optimal Configuration

The best-performing treatment (treatment **"a"**) used:
- ✅ High rotor length (8.5 cm)
- ✅ Low leg length (7.5 cm)
- ✅ Low leg width (3.2 cm)
- ✅ No paper clip

**Predicted flight time:** 2.47 seconds (95% CI: 2.31 – 2.63s)

## Technical Stack

- **Language:** R
- **Packages:** `tidyverse`, `dplyr`, `ggplot2`, `kableExtra`

## Full Report

<embed src="/docus/sta522-project2.pdf" width="100%" height="600" type="application/pdf">
