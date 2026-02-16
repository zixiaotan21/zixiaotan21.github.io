---
title: "FinCom: Financial Multi-Agent Systems"
image:
  path: /images/banner1.jpg
  thumbnail: /images/HDS.png
  caption: "Photo from [Vecteezy.com](https://www.vecteezy.com/)"
---

**FinCom** (Financial Committee) is a governed multi-agent framework that operationalizes the **Disagree-or-Commit (DoC)** protocol to embed structured dissent into financial AI decision-making. By reframing disagreement as a governance primitive rather than noise, FinCom improves accountability, transparency, and reasoning robustness in agentic financial systems.

## Part I: Problem Statement

<img src="/images/Agent_3.jpg" alt="poster" style="max-width: 40%;">

LLM-powered multi-agent systems are increasingly used in financial analysis, with specialized agents mirroring institutional trading desks. However, existing coordination schemes are vulnerable to **sycophancy**---agents conform to peer reasoning instead of evidence, leading to premature consensus and degraded outcomes. This mirrors behavioral-finance biases like herding and overreaction, undermining reliability in autonomous systems. Current frameworks either lack explicit governance or rely on rigid adversarial workflows. FinCom addresses this gap with a lightweight, prompt-only dissent mechanism that requires no fine-tuning.

## Part II: Multi-Agent Solution

![poster](/images/workflow_v3.png)

A central **Supervisor** orchestrates three ReAct-enabled specialist agents, each with role-specific tools:

- **Research Agent** --- Qualitative evidence gathering via web search, SEC filing analysis (10-K, 10-Q, 8-K), and real-time market data retrieval.
- **Quant Agent** --- Quantitative analysis including technical indicators (RSI, SMA, Bollinger Bands), strategy backtesting, and correlation analysis.
- **Risk Management Agent** --- Portfolio risk quantification through volatility/drawdown measurement, Value-at-Risk estimation, stress testing, and qualitative risk narratives.

Under the **DoC protocol**, each agent independently reviews peers' reasoning, flagging inconsistencies or unsupported claims. The Supervisor mediates exchanges until justified consensus or documented dissent is reached, ensuring the final recommendation reflects both critique and convergence.

## Part III: Evaluation

![poster](/images/eval_v1.png)

Evaluated on **120 handcrafted financial tasks** spanning research, quantitative, risk management, and joint investment plan categories using an LLM-as-a-Judge protocol:

- DoC achieved **71.3% average accuracy** vs. 61.6% for the consensus baseline.
- The largest gain was in **risk management tasks (+16.6 pp)**, where enforced dissent uncovered overlooked downside scenarios.
- **Quantitative tasks** reached the highest accuracy (89.7%), showing structured critique strengthens numerical reasoning.
- Qualitatively, DoC produced richer reasoning paths with better source attribution and fewer unsupported claims.

These results demonstrate that procedural dissent---embedding mandatory peer review at the prompt layer---improves correctness, transparency, and traceability in multi-agent financial reasoning.















