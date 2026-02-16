
FinCom: Disagree-or-Commit Protocol for Financial Multi-Agent Systems 


# What is Fincom
Abstract
Multi-agent systems powered by large language models (LLMs) are increasingly used for financial analysis and decision support. However, existing coordination schemes, especially those emphasizing consensus or debate, are vulnerable to sycophancy: agents conform to peer reasoning instead of evidence, leading to premature agreement and degraded outcomes. We introduce FinCom (Financial Committee), a governed multi-agent framework that operationalizes the Disagree-or-Commit (DoC) protocol to embed structured dissent into financial AI committees. A central Supervisor orchestrates three ReAct-enabled specialist agents---Research, Quantitative, and Risk---each equipped with role-specific tools for retrieval, computation, and stress testing. During deliberation, agents must either explicitly critique or commit to their peers’ reasoning before converging on a unified recommendation. Evaluated across 120 handcrafted financial tasks using an LLM-as-a-Judge protocol, DoC improves reasoning accuracy and risk awareness by up to 16 percentage points over a consensus-seeking baseline. By reframing disagreement as a governance primitive rather than noise, FinCom offers a lightweight, prompt-only recipe for improving accountability, transparency, and epistemic robustness in agentic financial systems.

# Part I: Problem Context

Introduction

Recent progress in Large Language Model (LLM)–based multi-agent systems is reshaping financial decision-making. Moving beyond single-agent assistants, researchers now design collaborative frameworks that mirror institutional trading desks, where specialized agents handle research, quantitative modeling, and risk management. Systems such as TradingAgents (Xiao 2025) and QuantAgent (Xiong 2025) show that role specialization enhances interpretability, reasoning diversity, and decision quality in complex markets.

Yet current frameworks either lack explicit governance making them prone to sycophancy—agents echoing peers rather than reasoning independently—leading to consensus collapse, or rely on rigid workflows to enforce debate (Xiao 2025). This mirrors behavioral-finance biases like herding and overreaction (Kahneman 1979, Barberis 2003), undermining reliability and accountability in autonomous systems.

We introduce FinCom, a light-weight, supervisor-based multi-agent framework that operationalizes the Disagree or commit (DoC) protocol to embed structured dissent and self-correction. FinCom’s Supervisor coordinates three expert agents: Research, Quantitative, and Risk. They each run a ReAct loop with constrained tools (e.g., SEC retrieval, quantitative analysis, stress testing). The DoC prompt extension compels agents to explicitly review, critique, or endorse their peers’ reasoning before submission, converting coordination into accountable deliberation.

Unlike adversarial debate frameworks (Du 2023, Liang 2024, Estornell 2024), DoC operates entirely at the prompt layer, requiring no fine-tuning and integrating easily with supervisor or swarm-based systems. On 120 financial tasks, DoC improved reasoning accuracy and risk awareness by up to 16 percentage points over a consensus baseline, demonstrating that procedural dissent strengthens both reliability and reproducibility in multi-agent financial reasoning.

# Part II: Multi-Agent System

Methodology
To emulate committee-style financial decision-making and mitigate single-model bias, we develop a multi-agent architecture comprising three domain-specialized agents—Research, Quant, and Risk Management—coordinated by a central Supervisor. Each agent is instantiated as a LangGraph node connected to Gemini-2.5-Flash with a role-specific system prompt and a constrained toolset. As the FinCom is being developed for an external business, we will refrain from sharing the specifics of each agent's prompts. All agents operate in a prompt-only configuration without task-specific fine-tuning, a deliberate choice that enhances adaptability across heterogeneous financial tasks while reducing data and compute requirements. Behavioral alignment is achieved through structured prompt conditioning and interaction rules. Agents follow a ReAct loop, while the Supervisor governs task decomposition, inter-agent coordination, validation, and synthesis. The modular architecture supports scalability, fault isolation, and targeted optimization of each component.

Execution Flow
Upon receiving a user query, the Supervisor performs intent parsing, decomposes the task into structured sub-tasks, and delegates them to the most relevant agents. Intermediate outputs are iteratively validated and refined before synthesis into a unified committee response. To improve deliberative robustness, agents participate in a structured DoC cycle: each agent independently reviews the reasoning and outputs of its peers, flagging inconsistencies, unsupported claims, or numerical anomalies. When disagreement arises, the detecting agent requests the Supervisor to prompt the originating agent for clarification or correction. The Supervisor mediates this exchange until consensus or justified dissent is reached, ensuring that the final decision reflects both critique and convergence.
Research Agent
The Research Agent performs qualitative evidence gathering and synthesis to produce structured insight reports. Operating through a ReAct reasoning loop, it constructs comprehensive financial narratives by integrating market context, regulatory filings, and real-time data. Its core responsibilities include:
Web Search: Capturing market trends, analyst opinions, and breaking news through web search with full source attribution.
SEC Analysis: Accessing and analyzing SEC filings (10-K, 10-Q, 8-K), extracting key sections (MD&A, Risk Factors, Financial Statements), and generating LLM-powered summaries of strategic direction and material risks.
Market Data: Retrieving real-time market data, including price aggregates, snapshots, and technical indicators (SMA, EMA, RSI, MACD).
Quant Agent
The Quant Agent performs quantitative market analysis with an emphasis on transparency and reproducibility. It integrates technical indicators, strategy backtesting, and correlation analysis to support data-driven trading decisions. Its responsibilities include:
Technical Indicators: Computing RSI, SMA, and Bollinger Bands to identify momentum and volatility patterns.
Strategy Backtesting: Testing trading strategies (SMA_CROSS, RSI_MEANREV) over historical data and reporting.
Correlation Analysis: Measuring inter-asset correlations to identify diversification opportunities and hedging candidates.
Risk Management Agent
The Risk Management Agent quantifies portfolio risk under baseline and stress conditions, translating quantitative analytics into actionable risk assessments. It combines historical market data analysis with scenario-based stress testing. Key functions include:

Volatility & Drawdown: Measuring annualized volatility and maximum drawdown from log returns.
Value-at-Risk (VaR): Estimating potential loss at 99% confidence using historical simulation.
Stress Testing: Conducting scenario analyses under adverse market conditions to quantify tail risk exposure.
Qualitative Factors: Integrating sector-specific risk narratives (regulatory, geopolitical, competitive) to complement quantitative metrics.


# Part III: Evaluation

Experiments
To evaluate our framework, we employ the LLM-as-a-Judge methodology, comparing agent outputs against a hand-labeled reference set for consistent and scalable assessment of reasoning quality across financial tasks. Two experimental conditions are tested: (1) agents operating under the Disagree or Commit (DoC) protocol, requiring verification and critique before commitment; and (2) a baseline with no DoC instruction. Outputs are compared to measure performance under identical conditions.
Dataset
We construct an internal evaluation dataset of 120 data points across research-heavy, quant-heavy, and risk management–oriented tasks, as well as investment plan evaluations engaging all three agents jointly, with each task having 30 data points. Each instance is manually designed, annotated, and verified to reflect realistic workflows. We avoid public financial AI benchmarks as they typically target narrow subtasks and lack structural similarity to our end-to-end decision workflows.

Evaluation

As illustrated in Figure 2, the evaluation follows a structured LLM-as-a-Judge pipeline. Each agent’s response is compared against reference answers using the evaluation prompt described in Appendix A. A score of 1 is assigned if the agent’s output includes all key elements without contradictions, and 0 otherwise. Each evaluation is repeated to mitigate stochastic variability, and averaged results are reported. Investment plan tasks are scored across three dimensions—risk assessment, trend detection, and news awareness—with the final score taken as their average.


Results

Across all domains, the DoC protocol improved decision quality and reasoning robustness compared to the consensus-based baseline. As shown in Table 1, agents operating under DoC achieved an average accuracy of 71.3%, outperforming the baseline’s 61.6%. The largest gain occurred in risk management tasks (+16.6 percentage points), where enforced dissent led agents to identify overlooked downside scenarios and quantify tail risks more accurately. Quantitative finance tasks achieved the highest overall accuracy (89.7%), showing that structured critique strengthens numerical reasoning in tool-based analyses. The research and investment plan tasks also benefited, showing fewer unsupported claims and improved source attribution.

Qualitatively, DoC deliberations produced richer, more diverse reasoning paths—agents cross-checked peer logic, surfaced counterexamples, and justified assumptions with verifiable evidence. In contrast, baseline agents relied entirely on the supervisor to catch any errors and make best decisions. These results confirm that embedding a mandatory check's improves correctness, transparency, and traceability in multi-agent reasoning.



Discussion
These results are by no means definitive.While FinCom shows measurable gains in hit rate across the board, it remains limited by its small, handcrafted dataset and the potential bias of the LLM-as-a-Judge evaluation.  Future work should expand to larger, human-validated datasets and cross-model evaluations (e.g., Gemini or Claude judges) for stronger external validity.

All agents currently share identical model priors, constraining epistemic diversity. Heterogeneous model ensembles and real-time simulation with market frictions (latency, liquidity) would yield more realistic behavior. While DoC reduces conformity, it adds computational cost; adaptive dissent, triggered only when reasoning entropy drops, could balance rigor and efficiency.

A natural next step is to benchmark DoC against structured deliberation methods such as Debate, Delphi, and self-consistency. Unlike these frameworks, DoC needs no fine-tuning or adversarial setup—its prompt-only layer makes it easily composable with supervisor or swarm systems. As a workshop-scale study, FinCom provides a practical starting point for integrating procedural dissent into multi-agent financial reasoning.

Conclusion

We presented FinCom, a governed multi-agent framework that operationalizes the Disagree or Commit (DoC) protocol to enhance reliability, diversity, and accountability in financial AI systems. By enforcing a structured dissent phase followed by unified commitment, FinCom transforms consensus-driven collaboration into a governance process that rewards evidence-based disagreement and disciplined convergence.

Empirical results demonstrate that DoC improves reasoning accuracy across financial domains—achieving up to 16% higher correctness than a consensus baseline—while producing more auditable and risk-aware recommendations. This validates our central hypothesis: productive conflict and commitment, when governed by explicit coordination rules, strengthens both the epistemic and behavioral robustness of LLM-based agentic systems.

Conceptually, DoC redefines collaboration among autonomous financial agents as a form of organizational governance rather than simple communication. It bridges insights from behavioral finance, organizational management, and AI safety by embedding procedural safeguards against conformity-driven errors. Through the Supervisor’s orchestration and structured ReAct loops, FinCom demonstrates how disagreement can be institutionalized to yield transparent, reproducible, and trustworthy outcomes.

Future work will extend this framework to larger and more heterogeneous agent collectives, integrate real-time market constraints such as liquidity and latency, and explore hybrid human–AI governance loops. By treating dissent not as noise but as a design principle, the DoC paradigm provides a foundation for resilient, auditable, and behaviorally grounded multi-agent financial intelligence.
