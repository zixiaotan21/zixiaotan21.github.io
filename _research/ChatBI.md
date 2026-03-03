---
title: "ChatBI: Conversational Business Intelligence"
image:
  path: /images/banner1.jpg
  thumbnail: /images/Tencent_logo.png
---

**ChatBI** is a conversational Business Intelligence system developed at **Tencent TEG**, enabling business users to query and visualize enterprise data through natural language---without requiring SQL expertise or manual table discovery. The system operates as a two-stage pipeline: a **Table Selection Agent** that identifies relevant data assets from a large-scale metadata catalog, followed by a **Data Agent** that performs analysis and generates interactive visualizations. Both stages are orchestrated by **DeepSeek-R1** in a ReAct loop over a suite of specialized tools.

## Part I: Business Problem

Enterprise BI workflows impose a steep technical barrier: analysts must manually navigate thousands of data tables, understand metadata schemas, and write SQL queries before any insight can be extracted. Business users without engineering backgrounds are effectively locked out, while even experienced analysts spend a disproportionate share of their time on data discovery rather than analysis.

Two core challenges drive the design of ChatBI:

| Challenge | Description |
|-----------|-------------|
| **Table Discovery** | Given a natural language question, the system must identify the relevant tables and fields from a catalog of thousands---requiring joint understanding of user intent, metadata semantics, and access permissions. |
| **Data Visualization** | Once the right data is located, producing clear and interactive visualizations demands chart type selection, field-to-axis mapping, aggregation logic, and rendering---steps that are tedious to perform manually. |

ChatBI addresses both challenges end-to-end: the Table Selection Agent handles data discovery, and the Data Agent handles analysis and rendering, together forming a seamless conversational BI interface.

## Part II: Architecture

<img src="/images/chatbi_arch.svg" alt="ChatBI Architecture" style="max-width: 100%;">

ChatBI is built on Tencent's internal **WeData / LLMApp** platform and deployed as a production conversational interface. The end-to-end pipeline is powered by **DeepSeek-R1** as the primary reasoning model, operating in a **ReAct loop** (Reason + Act) with function-call-style tool dispatch. A shared **PreProcess** module normalizes user input and injects structured context before each stage.

| Component | Role | Backbone Model |
|-----------|------|----------------|
| DeepSeek-R1 Orchestrator | ReAct loop, tool dispatch, intent parsing | DeepSeek-R1 |
| Table Selection Agent | Hybrid retrieval, schema recommendation | Qwen2.5-72B + DeepSeek-R1 |
| Data Agent | Statistical analysis, chart generation | Qwen2.5-72B + DeepSeek-R1 |

**Stage 1 --- Table Selection** resolves the user's natural language question into a set of recommended table schemas by combining keyword-based and embedding-based retrieval with permission filtering. **Stage 2 --- Data Generation** takes the retrieved schema and user intent to produce interactive charts or statistical reports through a dedicated toolset.

## Part III: Table Selection Agent

The Table Selection Agent is built on **ChatData**, Tencent's internal metadata-aware RAG system for enterprise data discovery. It exposes three tools to the DeepSeek-R1 orchestrator:

| Tool | Function |
|------|----------|
| `table-selection` | Hybrid retrieval (keyword + embedding) over the metadata catalog; returns recommended table names and column schemas |
| `asset-overview` | Queries data asset metadata: accessible databases, table counts, storage types, disk usage, and permission status |
| `superSQL` | Executes SQL against the Tencent SuperSQL engine---only triggered upon explicit user confirmation |

The `table-selection` tool itself encapsulates a three-component sub-pipeline, each powered by **Qwen2.5-72B**:

- **Keyword Extraction Agent** --- Rewrites the user's original question with historical context into a concise, retrieval-optimized keyword query, removing ambiguity and normalizing terminology.
- **DB/Table Name Recognition Agent** --- Detects any database or table names explicitly mentioned in the conversation, enabling direct lookup before invoking full retrieval.
- **Embedding Retriever** --- Performs semantic vector search over the metadata catalog with Top-K retrieval, followed by TF-IDF reranking over column fields. A filter layer removes test, temporary, and debug tables before returning results.

The three components feed jointly into `table-selection`, which applies hybrid search (embedding score weighted with keyword score), enforces permission-based filtering, and returns the top candidate schemas to the orchestrator. If the user's question concerns only asset-level queries---such as "how many tables do I have access to in database X"---the orchestrator routes directly to `asset-overview` instead.

## Part IV: Data Agent

The Data Agent takes the recommended table schemas and user intent as input and dispatches to three tools for analysis and visualization:

| Tool | Type | Function |
|------|------|----------|
| `chart-visualizer` | Interactive HTML (ECharts) | Renders line, bar, pie, and funnel charts from pre-processed data |
| `data-analysis` | Python sandbox | Data cleaning, pandas manipulation, statistical analysis, time-series modeling |
| `seaborn-drawer` | Static PNG | Heatmaps, box plots, violin plots, histograms---chart types not covered by `chart-visualizer` |

**Chart Visualization Pipeline**

`chart-visualizer` does not render charts directly from raw data. For each supported chart type, a dedicated **Qwen2.5-72B sub-agent** first parses the user's intent and extracts the necessary chart elements into a validated JSON Schema---specifying dimensions, metrics, aggregation methods, and axis mappings---before passing the structured configuration to the ECharts renderer.

| Agent | Chart Type | Extracted Fields |
|-------|-----------|-----------------|
| Pie Agent | Pie Chart (饼图) | `group`, `value`, aggregation type (sum / count / avg) |
| Line Agent | Line Chart (折线图) | x-axis (time / category), y-axis metric, time granularity |
| Bar Agent | Bar Chart (条形图) | category dimension, metric, optional secondary grouping |
| Funnel Agent | Funnel Chart (漏斗图) | pipeline stage dimension, conversion value |

**Data Analysis and Static Charts**

When the task requires computation beyond direct visualization---such as preprocessing, feature engineering, or statistical modeling---the orchestrator invokes `data-analysis`, a secure Python sandbox with access to numpy, pandas, scikit-learn, and statsmodels. For chart types outside the ECharts repertoire (e.g., box plots, violin plots, heatmaps), `seaborn-drawer` generates static `.png` outputs via Seaborn and Matplotlib as a fallback.
