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

<img src="/images/整体架构图2.png" alt="ChatBI Overall Architecture" style="max-width: 100%;">

ChatBI is organized into three stacked layers, each building on the one below.

**Layer 1 --- Knowledge Foundation (知识基建层)** sits at the base. Raw AMS data assets---structured metadata (table schemas, data cube definitions, user org hierarchies, historical SQL logs) and unstructured signals (product understanding protos, user behavior protos)---are ingested and processed into three purpose-built knowledge bases: a **business knowledge base** (业务知识库) for domain context and enriched metadata, a **compliance knowledge base** (合规知识库) encoding sensitivity levels and access policies, and a **benchmark evaluation set** (样本评测集) used for quality monitoring.

**Layer 2 --- Data Retrieval Agent (取数Agent)** handles the full query pipeline. An incoming user question first passes through **compliance checking** against the compliance knowledge base; non-compliant queries are rejected before any data is accessed. Compliant queries proceed to **intent classification**, which routes the request to the appropriate downstream handler and ultimately produces a **SQL query** for data retrieval.

**Layer 3 --- Data Analysis Agent (数据分析Agent)** receives the retrieved data and generates the final response. Depending on the task, it dispatches to a **Python sandbox** for statistical computation and preprocessing, a **chart rendering tool** (小马画图) for interactive BI charts, or **Seaborn** for static statistical visualizations.

## Part III: Knowledge Base

<img src="/images/知识库构建v2.png" alt="Knowledge Base Construction" style="max-width: 100%;">

The knowledge base underpinning ChatBI is constructed through a four-layer pipeline modeled after a data warehouse architecture.

**知识ODS层 (Raw Knowledge Layer)** is the source of truth. It contains two categories of data assets: structured knowledge---table and data cube metadata from 数平 and 数立方, field-level sensitivity classifications, and user org hierarchy---and unstructured knowledge in the form of product understanding and user behavior protos. These are ingested as-is without transformation.

**知识统一处理层 (Unified Processing Layer)** applies a sequential ETL pipeline: raw documents are **read** from their source systems, **cleaned** to remove noise and normalize formats, **structured** into a consistent schema, and finally **loaded** into the downstream knowledge stores. This layer ensures that all knowledge, regardless of origin, enters the system in a queryable, uniform representation.

**知识DWD层 (Knowledge Detail Layer)** organizes processed knowledge into three domain-specific stores:

| Knowledge Base | Contents |
|----------------|----------|
| **库表元信息知识库** | 数平 and 数立方 table metadata, field descriptions, and schema definitions |
| **合规知识库** | Table and field sensitivity levels, user organization and permission hierarchy |
| **业务知识库** | Enriched metadata annotations, data quality signals, and domain-specific context |

**知识应用层 (Application Layer)** exposes the knowledge bases to four downstream tasks: **合规判断** (compliance checking before data access), **数据解读** (LLM-augmented data interpretation), **text2sql** (natural language to SQL translation grounded in table metadata), and **数据洞察** (automated insight generation over retrieved data).

The retrieval mechanism for text2sql combines keyword-based and embedding-based search over the metadata catalog. A **Keyword Extraction Agent** rewrites the user question into a retrieval-optimized query; a **DB/Table Name Recognition Agent** handles explicit table references via direct lookup; and an **Embedding Retriever** performs Top-K semantic search followed by TF-IDF reranking over column fields. Hybrid scoring (embedding score weighted with keyword score) and permission-based filtering then produce the final set of candidate table schemas.

## Part IV: Multi-Agent Interaction

<img src="/images/Multi-Agent交互链路图3.png" alt="Multi-Agent Interaction Flow" style="max-width: 100%;">

ChatBI's end-to-end pipeline is executed by two cooperating agents: a **取数Agent** that handles query understanding and data retrieval, and a **数据分析Agent** that processes the retrieved data and generates the final response.

**取数Agent**

Every user question enters the 取数Agent and is first evaluated by a **compliance checker** (合规判断). Queries that violate data access policies---such as requests for restricted fields or unauthorized databases---are immediately rejected with a model-generated refusal. Compliant queries proceed to **intent classification** (意图分类), which routes the request along one of three paths:

| Intent | Handler | Output |
|--------|---------|--------|
| **元信息查询** | RAG retrieval over the knowledge base | Direct answer from knowledge base |
| **数据洞察** | RAG retrieval over the knowledge base | Insight narrative from retrieved context |
| **取数分析** | text2sql pipeline | Executable SQL query |

For **取数分析**, the intent classifier triggers the full text2sql sub-pipeline: keyword extraction, table name recognition, embedding-based retrieval, and hybrid reranking (described in Part III) together produce a SQL query that is executed against the Tencent SuperSQL engine.

**数据分析Agent**

Once data is retrieved, the 数据分析Agent selects the appropriate rendering tool based on the visualization task:

| Tool | Capabilities |
|------|-------------|
| **Python 沙箱** | Data cleaning and preprocessing, exploratory data analysis (EDA), feature engineering and statistical modeling |
| **小马画图 BI** | Interactive line charts (折线图), bar charts (条形图), pie charts (饼图), funnel charts (漏斗图) |
| **Seaborn 画图** | Static statistical charts: box plots (箱线图), histograms (直方图), violin plots (小提琴图), heatmaps (热力图) |

For interactive chart rendering, a dedicated **Qwen2.5-72B sub-agent** first extracts the necessary chart configuration---dimensions, metrics, aggregation methods, and axis mappings---into a validated JSON Schema before passing it to the chart renderer. This structured intermediate step decouples intent parsing from rendering and prevents hallucinated field names or invalid configurations from reaching the visualization layer.
