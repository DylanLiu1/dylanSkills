# AWS Certified Generative AI Developer – Professional (AIP-C01) 速学指南
# Rapid Study Guide — 中英双语版 / Bilingual Edition

> 基于 107 道真题题库整理 + 资深架构师视角补充。
> Based on a 107-question exam bank, plus senior-architect supplementary content.

---

## 如何使用本指南 / How to Use This Guide

**中文**：本指南按"考试概览 → 五大领域概要 → 核心知识点（按重要性排序）→ 逐题速查表 → 专家补充 → 备考策略"组织。每个知识点都给出**应试信号**（看到题目中的什么关键词，就该选什么答案），帮助你在考场上秒级定位答案。时间紧张时：先读 Tier 1 知识点 + 速查表，再扫 Tier 2/3。

**English**: This guide is organized as: Exam Overview → Domain Summaries → Core Knowledge Points (ranked by importance) → Question Quick-Reference → Expert Supplements → Exam Strategy. Every knowledge point includes **exam signals** (which keywords in a question point to which answer), so you can lock in answers in seconds. Short on time? Read Tier 1 + the quick-reference table first, then skim Tier 2/3.

---

# 第一部分：考试概览 / Part 1: Exam Overview

| 项目 Item | 内容 Details |
|---|---|
| 考试代码 Exam Code | AIP-C01（AWS Certified Generative AI Developer – Professional） |
| 时长 Duration | 180 分钟 / 180 minutes |
| 题量 Questions | 75 题（65 计分 + 10 不计分）/ 75 questions (65 scored + 10 unscored) |
| 题型 Format | 单选、多选 / Multiple choice & multiple response |
| 通过分数 Passing Score | 750 / 1000 |
| 费用 Cost | $300 USD |
| 目标考生 Target | 2 年以上生产级应用开发经验 + 1 年以上 GenAI 实操经验 / 2+ yrs production dev + 1+ yr hands-on GenAI |

## 领域权重 / Domain Weighting（官方考试指南）

| 领域 Domain | 权重 Weight | 题库题数 Questions in Bank |
|---|---|---|
| 1. 基础模型集成、数据管理与合规 / Foundation Model Integration, Data Management, and Compliance | **31%** | 27 |
| 2. 实施与集成 / Implementation and Integration | **26%** | 21 |
| 3. AI 安全、保障与治理 / AI Safety, Security, and Governance | **20%** | 24 |
| 4. GenAI 应用的运营效率与优化 / Operational Efficiency and Optimization | **12%** | 22 |
| 5. 测试、验证与故障排除 / Testing, Validation, and Troubleshooting | **11%** | 11 |

> **策略启示 / Strategy**: 领域 1+2 合计 57%，是得分主战场；领域 3 的 Guardrails/IAM 题套路固定、最易提分；领域 4+5 题量少但模式化极强，背熟套路即可全收。
> Domains 1+2 = 57% of the exam — your main battlefield. Domain 3 follows fixed patterns (easy points). Domains 4+5 are few but highly formulaic.

**官方考试指南 / Official Exam Guide**: https://docs.aws.amazon.com/aws-certification/latest/ai-professional-01/ai-professional-01.html

---

# 第二部分：五大领域总章概要 / Part 2: Domain Summaries

## 领域 1：基础模型集成、数据管理与合规（31%）
## Domain 1: Foundation Model Integration, Data Management, and Compliance (31%)

**中文概要**：本领域考"如何把数据安全、合规地喂给基础模型"。核心是 **RAG 全链路**：数据源（S3）→ 预处理（Glue/Comprehend）→ 分块（chunking）→ 嵌入（Titan Embeddings）→ 向量库（OpenSearch Serverless / Aurora pgvector / MemoryDB / Neptune）→ 检索（hybrid search、元数据过滤、rerank、query decomposition、GraphRAG）→ 生成（RetrieveAndGenerate，带引用）。合规线考数据驻留（cross-Region inference profile 地理代码）、访问控制（Lake Formation 列级授权、IAM ABAC）、审计（CloudTrail、Glue Data Catalog 血缘、Macie 分类）。

**English Summary**: This domain tests "how to feed data to foundation models safely and compliantly." The core is the **full RAG pipeline**: data sources (S3) → preprocessing (Glue/Comprehend) → chunking → embeddings (Titan) → vector stores (OpenSearch Serverless / Aurora pgvector / MemoryDB / Neptune) → retrieval (hybrid search, metadata filtering, rerank, query decomposition, GraphRAG) → grounded generation (RetrieveAndGenerate with citations). The compliance thread covers data residency (geo-scoped cross-Region inference profiles), access control (Lake Formation column-level grants, IAM ABAC), and audit (CloudTrail, Glue Data Catalog lineage, Macie classification).

**高频考点 / Hot Topics**: Knowledge Bases 全托管 RAG、分块策略选择（固定/语义/分层）、混合搜索、GraphRAG（Neptune）、数据驻留、S3 元数据体系。

## 领域 2：实施与集成（26%）
## Domain 2: Implementation and Integration (26%)

**中文概要**：考"怎么把 GenAI 应用搭出来"。四大主线：① **Bedrock API 选择**——InvokeModel（一次性）vs InvokeModelWithResponseStream（流式）vs Converse/ConverseStream（多轮对话、跨模型统一接口）；② **实时流式架构**——API Gateway WebSocket、Lambda response streaming、Transcribe streaming partial results；③ **多智能体**——supervisor/collaborator 模式、AgentCore、MCP server（Streamable HTTP + OAuth 2.1）；④ **编排与配置**——Step Functions（Standard vs Express、Parallel/Map、waitForTaskToken、256KB 状态配额→S3 引用）、AppConfig（动态配置/feature flags/A/B 测试）、Bedrock Flows、Prompt Management。

**English Summary**: Tests "how to build GenAI applications." Four threads: ① **Bedrock API selection** — InvokeModel (one-shot) vs InvokeModelWithResponseStream (streaming) vs Converse/ConverseStream (multi-turn, model-agnostic); ② **real-time streaming architectures** — API Gateway WebSocket, Lambda response streaming, Transcribe streaming partial results; ③ **multi-agent systems** — supervisor/collaborator pattern, AgentCore, MCP servers (Streamable HTTP + OAuth 2.1); ④ **orchestration & configuration** — Step Functions (Standard vs Express, Parallel/Map, waitForTaskToken, 256KB state quota → S3 references), AppConfig (dynamic config/feature flags/A-B testing), Bedrock Flows, Prompt Management.

**高频考点 / Hot Topics**: 流式 API 选型、WebSocket vs 轮询、supervisor 多智能体、Step Functions 状态管理、AppConfig 动态路由、Converse API 多轮上下文。

## 领域 3：AI 安全、保障与治理（20%）
## Domain 3: AI Safety, Security, and Governance (20%)

**中文概要**：套路最固定、最好拿分的领域。一条主线：**Bedrock Guardrails 六大能力**——内容过滤（hate/sexual/violence/insults/misconduct/prompt attack，强度 low/medium/high）、denied topics（拒绝话题）、敏感信息过滤（PII：block/mask/detect，输入 vs 输出可不同动作）、自定义词过滤、contextual grounding（防幻觉）、guardrail profile（跨区域一致部署）。配合：IAM 条件键（强制 GuardrailIdentifier/ModelId）、SCP（Organizations 最强边界，优先于 IAM）、VPC endpoint/PrivateLink（私有访问）、CloudTrail + invocation logging（双层审计）、A2I/waitForTaskToken（人工兜底）。

**English Summary**: The most formulaic, easiest domain to score. One main thread: **the six Bedrock Guardrails capabilities** — content filters (hate/sexual/violence/insults/misconduct/prompt attack at low/medium/high strength), denied topics, sensitive information filters (PII: block/mask/detect, with different actions for input vs output), custom word filters, contextual grounding (anti-hallucination), and guardrail profiles (consistent cross-Region deployment). Plus: IAM condition keys (enforce GuardrailIdentifier/ModelId), SCPs (strongest Organizations boundary, evaluated before IAM), VPC endpoints/PrivateLink (private access), CloudTrail + invocation logging (two-layer audit), and A2I/waitForTaskToken (human-in-the-loop).

**高频考点 / Hot Topics**: block vs mask vs detect、denied topics vs word filters vs content filters 的分工、SCP + guardrail 集中治理、跨区域 guardrail 与数据驻留、提示注入纵深防御。

## 领域 4：运营效率与优化（12%）
## Domain 4: Operational Efficiency and Optimization (12%)

**中文概要**：考"让 GenAI 应用更快、更省、更稳"。三组对立概念是全部考点：① **On-Demand vs Provisioned Throughput**（波动流量无固定成本 vs 稳定高吞吐有保障容量；用预置吞吐必须用返回的**模型 ARN** 调用！）；② **限流治理**——cross-Region inference profile 分摊流量、指数退避+jitter 重试、熔断器、token batching；③ **可观测性**——invocation logging 到 CloudWatch/S3、metric filter 提取 token 指标、**anomaly detection 动态基线**（vs 静态阈值）、EMF 自定义指标、Application Insights、X-Ray 注解追踪。加分项：intelligent prompt routing（按复杂度自动选模型省钱）、PerformanceConfigLatency=optimized。

**English Summary**: Tests "making GenAI apps faster, cheaper, steadier." All content reduces to three pairs of opposites: ① **On-Demand vs Provisioned Throughput** (bursty traffic with no fixed cost vs guaranteed capacity for steady high throughput; provisioned throughput only works when you invoke with the returned **model ARN**!); ② **throttle management** — cross-Region inference profiles, exponential backoff with jitter, circuit breakers, token batching; ③ **observability** — invocation logging to CloudWatch/S3, metric filters for token metrics, **anomaly detection with dynamic baselines** (vs static thresholds), EMF custom metrics, Application Insights, X-Ray annotations. Bonus: intelligent prompt routing (auto model selection by complexity), PerformanceConfigLatency=optimized.

**高频考点 / Hot Topics**: provisioned ARN 陷阱、anomaly detection vs 静态阈值、重试三件套（退避+jitter+熔断）、跨区域推理削峰、并行化降延迟（Step Functions Parallel）。

## 领域 5：测试、验证与故障排除（11%）
## Domain 5: Testing, Validation, and Troubleshooting (11%)

**中文概要**：考"怎么证明 GenAI 应用足够好、上线后不退化"。核心四件套：① **Bedrock Model Evaluation**——JSONL 数据集放 S3、固定同一 judge 模型（LLM-as-a-judge）保证可比性、数值评分做 CI/CD 质量门禁；② **检索评估 vs 端到端评估**——retrieve-only 看检索、retrieve-and-generate 看全链路；③ **人工兜底**——A2I 按置信度把高风险交互路由给人工；④ **故障排查套路**——超上下文窗口→语义分块+动态检索；输出不一致→prompt 版本化+固定推理参数+量化指标回归测试；跨语言不一致→语义等价多语言测试集+相似度阈值。

**English Summary**: Tests "how to prove a GenAI app is good enough and doesn't regress." Four core pieces: ① **Bedrock Model Evaluation** — JSONL datasets in S3, a single fixed judge model (LLM-as-a-judge) for comparability, numeric scores as CI/CD quality gates; ② **retrieval-only vs end-to-end evaluation** — retrieve-only assesses retrieval, retrieve-and-generate assesses the full pipeline; ③ **human fallback** — A2I routes high-risk interactions to humans by confidence; ④ **troubleshooting patterns** — context-window overflow → semantic chunking + dynamic retrieval; inconsistent outputs → prompt versioning + fixed inference parameters + metric-based regression tests; cross-language inconsistency → semantically equivalent multilingual test sets + similarity thresholds.

**高频考点 / Hot Topics**: judge 模型固定原则、评估作业输入格式（JSONL/S3）、质量门禁接 CI/CD、A2I 混合评估、语义分块治"超长文档截断"。

---

# 第三部分：核心知识点详解（按重要性排序）
# Part 3: Core Knowledge Points (Ranked by Importance)

> 排序依据：题库出现频率 × 考试权重。Tier 1 = 必考必背；Tier 2 = 高频；Tier 3 = 常见。
> Ranking = frequency in the question bank × exam weight. Tier 1 = must-know; Tier 2 = high-frequency; Tier 3 = common.

---

## 🥇 TIER 1 — 必考核心 / Must-Master

---

### KP1. Amazon Bedrock Knowledge Bases 与 RAG 全链路 ⭐⭐⭐⭐⭐
### Amazon Bedrock Knowledge Bases & the RAG Pipeline

**中文详解**：Knowledge Bases 是 Bedrock 的**全托管 RAG 服务**：自动完成数据摄取（S3）、分块、嵌入生成、向量索引、检索和上下文组装。考试里凡是"最少运维开销/最少开发量 + 需要让模型基于企业数据回答"的场景，答案几乎都是 Knowledge Bases。关键子能力：
- **检索 API**：`Retrieve`（只检索，自己拼 prompt）vs `RetrieveAndGenerate`（检索+生成一站式，原生返回**引用 citations**）vs `RetrieveAndGenerateStream`（流式版本）。
- **来源归因 source attribution**：生成结果携带源文档/片段引用——看到"可溯源/引用来源/审计 claims"必选。
- **Rerank**：Knowledge Bases 内置 reranking 配置（托管 reranker 模型）是"语义相似但上下文不相关→幻觉"的**最少运维**解；单独调 Rerank API 也行但要改代码。
- **Query decomposition**：把复杂多意图查询拆成子查询——对应"减少语义稀释/提高复杂查询检索精度"。
- **GraphRAG + Neptune Analytics**：多跳实体关系（投资工具↔板块↔监管）问题，向量搜索做不好，图遍历才行。
- **元数据过滤 metadata filtering**：检索过宽（过多文档类型/时间跨度）时，用 S3 对象元数据收窄候选集，是"最小架构改动"解。
- **隔离模式**：强租户/合规隔离（每家酒店/每个部门）→ 每租户一个 Knowledge Base + IAM，优于集中式+查询后过滤。

**English Deep Dive**: Knowledge Bases is Bedrock's **fully managed RAG service**: ingestion (S3), chunking, embedding, vector indexing, retrieval, and context assembly are all managed. Whenever a question says "least operational overhead / least development effort + answer from enterprise data," the answer is almost always Knowledge Bases. Key sub-capabilities: `Retrieve` (retrieval only) vs `RetrieveAndGenerate` (one-step with native **citations**) vs `RetrieveAndGenerateStream`; **source attribution** for auditable claims; **built-in reranking** as the lowest-overhead fix for "semantically similar but contextually irrelevant" retrievals; **query decomposition** for complex multi-intent queries; **GraphRAG + Neptune Analytics** for multi-hop entity relationships; **metadata filtering** as the minimal-change fix for overly broad retrieval; and **per-tenant Knowledge Bases + IAM** for hard isolation.

**应试信号 → 答案 / Exam Signals → Answers**:
| 题目关键词 Signal | 选这个 Pick |
|---|---|
| "least operational overhead" + 企业数据问答 | Bedrock Knowledge Bases |
| 检索结果"语义相似但上下文无关"、幻觉 | KB 内置 reranking 配置（不要选自建 SageMaker 排序端点） |
| "链接到源文档 / citations / 可审计 claims" | source attribution / RetrieveAndGenerate |
| 复杂多意图查询、语义稀释 | query decomposition |
| 多跳关系（A 影响 B 影响 C）、实体互联 | GraphRAG + Neptune Analytics |
| 检索范围太宽、要"最小改动" | metadata-aware filtering |
| 按租户/部门硬隔离数据 | 每租户一个 Knowledge Base + IAM |

**相关题目 / Related Questions**: Q70, Q105(领域1), Q81, Q99, Q104×3, Q107(领域1), Q96, Q98(测试), Q75, Q78

**官方文档 / References**:
- Knowledge Bases: https://docs.aws.amazon.com/bedrock/latest/userguide/knowledge-base.html
- Reranking: https://docs.aws.amazon.com/bedrock/latest/userguide/kb-rerank.html
- Query decomposition: https://docs.aws.amazon.com/bedrock/latest/userguide/kb-test-retrieve-decomp.html
- GraphRAG: https://docs.aws.amazon.com/bedrock/latest/userguide/knowledge-base-graphrag.html
- Metadata filtering: https://docs.aws.amazon.com/bedrock/latest/userguide/kb-metadata-filtering.html

---

### KP2. 分块策略与嵌入 / Chunking Strategies & Embeddings ⭐⭐⭐⭐⭐

**中文详解**：分块是 RAG 质量的第一杠杆，题库反复考四种策略的选择：
- **固定分块 fixed-size**：简单但会**切断语义**——"法律论证被截断/条文引用断链"的根因，通常是题目里的**错误现状**而非答案。
- **语义分块 semantic**：按意义边界切分（如 breakpoint percentile 95% + 句缓冲）——治"复杂查询/长文档超上下文窗口被截断"。
- **分层分块 hierarchical**：父块（如 1000 token）存上下文、子块（如 200 token）做精确匹配、重叠 50 token——治"跨章节引用（方法/结果/讨论）需要父级上下文"。命中子块时带回父块。
- **上下文分块（按文档结构布局）**：财报"表格数字↔解释文字"关联场景，按章节/表格/段落切。
- **改分块必须重新生成嵌入**（re-embed），向量与新 chunk 对齐。
- **嵌入模型**：Titan Embeddings 托管生成向量；文档与查询必须用**同一嵌入模型**保证向量空间一致。

**English Deep Dive**: Chunking is the #1 lever of RAG quality. **Fixed-size** chunking severs semantics (usually the *problem* in the question, not the answer). **Semantic** chunking splits on meaning boundaries (breakpoint percentile ~95% + sentence buffer) — the fix for context-window truncation on long docs. **Hierarchical** chunking pairs small child chunks (~200 tokens, precise matching) with parent chunks (~1000 tokens, context), ~50-token overlap — the fix for cross-section references (methods/results/discussion) needing parent context. **Layout-aware chunking** preserves table↔narrative associations in financial reports. Re-chunking requires **re-embedding**. Documents and queries must use the **same embedding model**.

**应试信号 → 答案 / Exam Signals → Answers**:
| 题目关键词 Signal | 选这个 Pick |
|---|---|
| 固定分块切断论证/引用 | 改语义分块 + 重新生成嵌入 |
| 跨章节（方法/结果/讨论）引用不相关 | hierarchical chunking（父1000/子200/重叠50） |
| 50–200页文档超上下文窗口、输出截断 | 语义分块 + RetrieveAndGenerate 动态检索 |
| 表格数字与解释文字要保关联 | 按文档结构布局的上下文分块 |
| 提高向量维度能解决检索质量？ | ❌ 干扰项——只增成本延迟 |

**相关题目 / Related Questions**: Q82(领域1), Q83, Q77, Q64, Q101(测试)

**官方文档 / References**: https://docs.aws.amazon.com/bedrock/latest/userguide/kb-chunking.html

---

### KP3. Bedrock Guardrails 六大能力 ⭐⭐⭐⭐⭐
### The Six Guardrails Capabilities

**中文详解**：Guardrails 在推理路径**内联**生效（低延迟、可满足 200ms 要求），同时作用于**输入和输出**。六大能力及分工（考试就是考分工）：
1. **内容过滤 content filters**：hate/sexual/violence/insults/misconduct/prompt attack，强度 low/medium/high。面向客户金融场景推荐 **medium**（high 误报泛滥）；防提示注入用 **high**。
2. **Denied topics 拒绝话题**：整类阻断领域话题（投资建议、个股推荐、保证收益）——配**定义 + 示例短语**提高准确率。
3. **敏感信息过滤 sensitive info (PII)**：三种动作——**block**（拦截，用于输入端最强防护）、**mask**（打码脱敏，用于输出端；开发测试期用 mask 便于验证规则）、**detect**（只标记不阻断——"只通知不阻断"场景）。输入输出可配不同动作。
4. **自定义词过滤 word filters**：屏蔽**竞争对手名称**等具体词表——content filter 干不了这个（经典陷阱）。
5. **Contextual grounding 上下文接地**：防幻觉，验证输出是否基于给定文档；**高阈值**强制有据可依。
6. **Guardrail profile**：跨区域一致部署同一护栏配置（无配置漂移）；但注意 **guardrail 是 Region 级资源**——数据驻留场景须每个 Region 部署本地副本，cross-Region guardrail 违反驻留。

集中治理套路：CloudFormation/StackSets 把 guardrail 部署到所有账户 + **SCP 强制调用必须带 guardrail ID**（SCP 可校验请求参数，权限边界不行）。

**English Deep Dive**: Guardrails execute **inline** on the inference path (low latency), applying to **both input and output**. The six capabilities and their division of labor: ① **content filters** (hate/sexual/violence/insults/misconduct/prompt attack at low/medium/high — medium for customer-facing finance; high against prompt injection); ② **denied topics** for blocking whole topic classes (investment advice) with definitions + sample phrases; ③ **sensitive info filters** with three actions — **block** (input-side max protection), **mask** (output-side redaction; use mask in dev to validate rules), **detect** (flag only, for "notify without blocking"); ④ **custom word filters** for competitor names (content filters can't do this — classic trap); ⑤ **contextual grounding** against hallucination (high threshold = evidence-based only); ⑥ **guardrail profiles** for consistent multi-Region application — but guardrails are Regional resources, so data-residency scenarios need a local copy per Region. Centralized governance: deploy via CloudFormation StackSets + SCPs enforcing guardrail IDs on invocations.

**应试信号 → 答案 / Exam Signals → Answers**:
| 题目关键词 Signal | 选这个 Pick |
|---|---|
| 禁止谈论某话题（投资建议） | denied topics + 定义 + 示例短语 |
| 屏蔽竞争对手名字 | 自定义词过滤（不是 content filter！） |
| PII 不进模型 / 响应脱敏 | 敏感信息过滤：输入 block / 输出 mask |
| "只通知不阻断"（霸凌语言） | insults 过滤器 + 动作 = detect |
| 防幻觉/输出须有据可依 | contextual grounding + 高阈值 |
| 防提示注入 + 跨区域一致 + 审计 | 内容过滤 high + guardrail profile + CloudWatch Logs |
| 最小化误报 | medium 灵敏度、单一精细调优 guardrail（不要分层多个） |
| 测试期验证 PII 规则、生产最强防护 | 测试 mask → 生产 block，每 Region 各部署一份 |

**相关题目 / Related Questions**: Q107(AI安全)×2, Q102(AI安全)×2, Q72, Q93(AI安全), Q94(AI安全), Q100(UGC), Q101(注入), Q87(医疗), Q79, Q98×2

**官方文档 / References**:
- Guardrails: https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html
- Sensitive info filters: https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-sensitive-info.html
- Contextual grounding: https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-contextual-grounding.html

---

### KP4. Bedrock 推理 API 与流式体系 ⭐⭐⭐⭐⭐
### Bedrock Inference APIs & Streaming

**中文详解**：API 选型是领域 2 的送分题，对照表背下来：
- **InvokeModel**：一次性返回完整响应——非交互、后台任务。
- **InvokeModelWithResponseStream**：token 级流式输出——"实时/逐字符展示/降低感知延迟/响应 15–45 秒"必选。
- **Converse / ConverseStream**：**多轮对话**统一接口，跨模型（Claude/Titan/Llama…）消息格式一致；上下文通过请求里的 **messages 数组**显式传递，无需外部状态存储；跨 Lambda(Python)/EKS(JS) 等多环境用同一接口 + IAM 角色认证。
- **配套流式前端**：API Gateway **WebSocket API**（服务端主动推送、双向、高并发长连接）＞ HTTP 轮询；**Lambda response streaming**（一产生就返回客户端）；**Transcribe streaming + partial results**（说话人没说完就出片段，亚秒级语音链路的关键）。
- 典型实时链路：Transcribe streaming(partial) → Bedrock InvokeModelWithResponseStream → API Gateway WebSocket 推送。
- **禁止**：前端直连 Bedrock 持 IAM 凭证（安全违规）。

**English Deep Dive**: API selection is free points in Domain 2: **InvokeModel** (one-shot, background jobs); **InvokeModelWithResponseStream** (token-level streaming — pick for "real-time / character-by-character / perceived latency"); **Converse/ConverseStream** (unified **multi-turn** interface across models; context passed explicitly via the **messages array**, no external state store; works identically across Lambda/EKS with IAM roles). Streaming front-ends: API Gateway **WebSocket API** (server push, bidirectional, high-concurrency long connections) beats polling; **Lambda response streaming**; **Transcribe streaming with partial results** (fragments before the speaker finishes — the key to sub-second voice pipelines). Canonical real-time chain: Transcribe streaming(partial) → InvokeModelWithResponseStream → WebSocket push. Never let browsers call Bedrock directly with IAM credentials.

**应试信号 → 答案 / Exam Signals → Answers**:
| 题目关键词 Signal | 选这个 Pick |
|---|---|
| 逐字符实时展示、数千并发、响应 15–45s | WebSocket API + Lambda + InvokeModelWithResponseStream |
| 语音助手端到端 <1 秒、边说边转写 | Transcribe streaming partial results + 流式推理 + WebSocket |
| 多轮对话、跨语言/跨计算环境统一 | Converse API + messages 数组 + IAM 角色 |
| 双向流式通信、托管 | API Gateway WebSocket（不要选轮询/ECS 自建 socket） |

**相关题目 / Related Questions**: Q94(实施), Q92, Q98(实施), Q101(实施), Q85(流式API设计), Q68(测试)

**官方文档 / References**:
- Converse API: https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference.html
- Streaming: https://docs.aws.amazon.com/bedrock/latest/userguide/inference-invoke.html

---

### KP5. Cross-Region Inference 与数据驻留 ⭐⭐⭐⭐⭐
### Cross-Region Inference Profiles & Data Residency

**中文详解**：这是题库中重复出现 4+ 次的超级考点。规则：
- **Cross-Region inference (CRI) profile**：调用时 modelId 用 **inference profile ID**（带地理代码，如 `eu.amazon.nova-pro-v1:0`），Bedrock 自动在**该地理范围内**的多 Region 间路由/削峰/故障转移——免自建 failover、免预留容量、按需计费无固定成本。
- **数据驻留**：选**地理范围限定**的 profile（EU 范围 profile 只路由到欧洲 Region）——"欧洲数据不出欧洲"的合规解。
- **SCP 治理**：SCP 优先于 IAM（AdministratorAccess 也过不了 SCP 显式拒绝）；SCP 必须**显式放行 inference profile**；扩展 SCP 允许 `eu-*` profile = 精确治理 + 驻留兼得。
- **私有访问**：Bedrock **interface VPC endpoint (PrivateLink)** 保证流量不出 AWS 私网；NAT 网关/公网端点一律违规。
- 反模式：自建 Lambda/EC2 路由层、把重试选区逻辑写进应用（有误配跨境风险）、改去 SageMaker 托管。

**English Deep Dive**: A super-topic appearing 4+ times in the bank. Rules: a **CRI profile** (geography-coded ID, e.g. `eu.amazon.nova-pro-v1:0`) lets Bedrock automatically route/throttle-shift/fail over across Regions **within that geography** — no self-built failover, on-demand pricing with no fixed cost. **Data residency**: pick a geo-scoped profile (EU profile routes only to EU Regions). **SCP governance**: SCPs override IAM (even AdministratorAccess can't pass an explicit SCP deny); SCPs must explicitly allow the inference profile; allowing `eu-*` profiles = precise governance + residency. **Private access**: Bedrock interface VPC endpoints (PrivateLink); NAT gateways/public endpoints always violate. Anti-patterns: self-built Lambda/EC2 routing layers, app-level retry/region-selection logic, moving to SageMaker hosting.

**应试信号 → 答案 / Exam Signals → Answers**:
| 题目关键词 Signal | 选这个 Pick |
|---|---|
| 峰值限流 + 数据驻留 + 运维最简 | geo-scoped cross-Region inference profile + 分区 API Gateway |
| SCP 拒绝 CRI 调用（资源落在他区） | 扩展 SCP 放行 `eu.amazon.*` / `eu-*` inference profile |
| 请求走私有网络 + 留在指定区域 | CRI profile + Bedrock VPC endpoint |
| 全球时区晚高峰限流、低谷无固定成本 | CRI 分散流量（on-demand），不要 provisioned throughput |

**相关题目 / Related Questions**: Q106(AI安全), Q90(AI安全), Q105(运营)×2, Q104(运营), Q102(领域1), Q103(运营)

**官方文档 / References**: https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference.html

---

### KP6. Provisioned Throughput vs On-Demand ⭐⭐⭐⭐
### Provisioned Throughput vs On-Demand

**中文详解**：
- **Provisioned Throughput (PT)**：购买 Model Units 预留容量——关键词"稳定高吞吐/批处理/延迟一致/消除限流与冷启动"。**有固定小时费**，低谷也计费。
- **On-Demand**：按量付费、无固定成本——关键词"流量波动/时区高峰/低谷无固定费用"。
- ⚠️ **经典陷阱（考了 2 次）**：买了 PT 但代码用基础 modelId（如 `anthropic.claude-v2`）调用 → 流量走 on-demand 池 → "预置容量闲置 + 按需被限流"。**解法：把 modelId 换成 CreateProvisionedModelThroughput 返回的预置模型 ARN**。
- 加 MU 不解决路由错误；退避重试只是止痛。

**English Deep Dive**: **PT** reserves Model Units for guaranteed capacity — signals: "steady high throughput / consistent latency / eliminate throttling & cold starts"; it bills hourly even when idle. **On-Demand** has no fixed cost — signals: "bursty traffic / no fixed cost in troughs." ⚠️ **Classic trap (asked twice)**: bought PT but invoking with the base modelId → traffic goes to the on-demand pool → "provisioned capacity idle + on-demand throttled." **Fix: set modelId to the provisioned model ARN** returned by CreateProvisionedModelThroughput. Adding MUs doesn't fix routing; retries only mask symptoms.

**相关题目 / Related Questions**: Q106(运营)×2, Q91, Q102(AI安全), Q76, Q93(实施), Q104(运营-全球学生)

**官方文档 / References**: https://docs.aws.amazon.com/bedrock/latest/userguide/prov-throughput.html

---

## 🥈 TIER 2 — 高频考点 / High-Frequency

---

### KP7. 多智能体架构：Supervisor + Collaborator、AgentCore、MCP ⭐⭐⭐⭐
### Multi-Agent Architectures

**中文详解**：
- **Supervisor + Collaborator 模式**（考 3 次）：一个 supervisor agent 做意图分类与路由，多个 collaborator agent 各管一个领域（临床/保险/预约/理赔），各自挂**独立 Knowledge Base + IAM 隔离**。新功能 = 新 collaborator + 新 KB，无需重构。
- **Bedrock AgentCore**：托管 agent 记忆、会话感知推理、身份与事件处理——"跨交互记忆 + 会话状态 + 同步/事件双模式 + 最具扩展性"的答案。
- **MCP server**：远程 MCP 用 **Streamable HTTP 传输**（STDIO 只适合本地进程）；Lambda 托管 + API Gateway 代理 + **Cognito OAuth 2.1** 做用户级授权（只授权 agent ≠ 用户授权）。
- **Agents tracing**：端到端推理步骤 + 工具调用轨迹——"透明/展示推理过程/审计"场景。

**English Deep Dive**: **Supervisor + Collaborator** (asked 3×): one supervisor classifies intent and routes to domain collaborator agents, each with its own **isolated Knowledge Base + IAM**. New capability = new collaborator + KB. **Bedrock AgentCore** manages agent memory, session-aware reasoning, identity, and event handling. **MCP servers**: remote MCP uses **Streamable HTTP** (STDIO is local-only); host on Lambda + API Gateway with **Cognito OAuth 2.1** for user-level authorization. **Agents tracing** exposes reasoning steps and tool calls for transparency/audit.

**应试信号 → 答案**: 多部门助手+数据隔离+易扩展 → supervisor/collaborator + 每部门 KB；agent 记忆与会话 → AgentCore；MCP 远程授权 → Streamable HTTP + OAuth 2.1。

**相关题目**: Q75, Q105(实施-医疗), Q85(实施-记忆), Q98(MCP), Q73
**参考**: https://docs.aws.amazon.com/bedrock/latest/userguide/agents-multi-agent-collaboration.html

---

### KP8. AWS AppConfig：动态配置与 Feature Flags ⭐⭐⭐⭐

**中文详解**：AppConfig 是"**不改代码/不重部署就改行为**"的御用答案（考 4 次）：动态配置、JSON schema 校验、feature flags（A/B 测试/渐进发布）、分阶段 rollout、自动回滚、**AppConfig Agent**（Lambda 扩展层，本地缓存低延迟读配置）。场景：多 FM 动态路由规则（按用户层级/监管区/成本）、按客户等级切模型、prompt 模板版本门控。干扰项：Parameter Store（无 rollout/校验/回滚）、环境变量（需重启、无校验）、DynamoDB/ElastiCache（要自建 flag 与轮询）。

**English Deep Dive**: AppConfig is the canonical answer for "change behavior without redeploying" (asked 4×): dynamic config, JSON schema validation, feature flags (A/B, progressive rollouts), staged deployment, automatic rollback, and the **AppConfig Agent** Lambda extension (local caching). Scenarios: dynamic multi-FM routing rules, per-tier model switching, prompt-template version gating. Distractors: Parameter Store (no rollout/validation/rollback), env vars (require restart), DynamoDB/ElastiCache (DIY flags).

**相关题目**: Q74, Q106(实施), Q72(运营), Q104(实施), Q90(实施)
**参考**: https://docs.aws.amazon.com/appconfig/latest/userguide/what-is-appconfig.html

---

### KP9. 可观测性：CloudWatch 全家桶 + X-Ray + CloudTrail ⭐⭐⭐⭐
### Observability: CloudWatch, X-Ray, CloudTrail

**中文详解**（考 6+ 次，模式极固定）：
- **Model invocation logging**：Bedrock 原生把调用日志（含 token 计数、prompt/response）投递到 **CloudWatch Logs 或 S3**——一切监控的数据源。
- **Metric filters**：从日志提取按工具/模型维度的结构化指标，免自建管道。
- **Anomaly detection 异常检测**：自动建动态基线——"阈值随流量自动调整/对比基线检测退化"必选；**静态阈值 = 干扰项**。
- **EMF (embedded metric format)** 自定义指标 + 维度（请求类型/用户分群）：细粒度 GenAI 信号（推荐质量/token/延迟）。
- **Application Insights**：EC2 应用自动观测 + 指标日志关联根因。
- **X-Ray**：跨服务分布式追踪定位延迟来源；**注解 annotations** 标模型特征（model ID、推理参数、Region）做关联分析。CloudTrail 是审计不是追踪！
- **CloudTrail / CloudTrail Lake**：API 级审计（谁、何时、调了什么）；Lake 提供长期可查询集中审计。审计双层 = CloudTrail（API）+ invocation logging（prompt/response 明细）。
- **Managed Grafana zero-ETL**：多角色自定义仪表盘的最少运维方案。

**English Deep Dive** (asked 6+ times, formulaic): **Invocation logging** streams call logs (tokens, prompts/responses) to CloudWatch Logs or S3 — the data source for everything. **Metric filters** extract per-tool metrics without pipelines. **Anomaly detection** builds dynamic baselines — always pick it for "thresholds that auto-adjust / detect degradation vs baseline"; static thresholds are distractors. **EMF** custom metrics with dimensions slice GenAI signals. **Application Insights** auto-monitors EC2 apps and correlates root causes. **X-Ray** traces cross-service latency; **annotations** tag model characteristics. CloudTrail is audit, not tracing! Two-layer audit = CloudTrail (API) + invocation logging (content). **Managed Grafana zero-ETL** for multi-role dashboards.

**相关题目**: Q48, Q100(运营-token), Q104(运营)×2, Q88, Q103(运营), Q80(AI安全), Q105(运营-仪表盘)
**参考**: https://docs.aws.amazon.com/bedrock/latest/userguide/model-invocation-logging.html ｜ https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Anomaly_Detection.html

---

### KP10. Step Functions 编排模式 ⭐⭐⭐⭐
### Step Functions Orchestration Patterns

**中文详解**：
- **Standard vs Express**：长运行、有状态、可审计（训练流程、人工审批）→ **Standard**；短平快高并发 → Express。
- **Parallel / Map state**：相互独立的多次 FM 调用**并行化**——"45 秒串行压到 10 秒"的唯一正解；短期密钥并行刷新用 Map。
- **waitForTaskToken（回调等待）**：**human-in-the-loop 标准做法**——人工审批暂停工作流，SendTaskSuccess 继续，状态转移全程可审计；也用于"等待用户澄清输入"。
- **256KB 状态配额**：中间推理输出超限 → **大对象存 S3，状态间用 ResultPath/ResultSelector 只传引用（URI/key）**。
- 经典事件驱动 MLOps：S3 上传事件 → Lambda → Step Functions → SageMaker Pipelines 重训练重部署。

**English Deep Dive**: **Standard** for long-running, auditable, stateful flows (training, human approval); Express for short high-rate ones. **Parallel/Map** states parallelize independent FM calls — the only way to cut 45s serial to 10s. **waitForTaskToken** is the standard human-in-the-loop pattern (pause for approval, resume via SendTaskSuccess, fully auditable). **256KB state quota**: store large intermediate outputs in S3 and pass only references via ResultPath/ResultSelector. Classic event-driven MLOps: S3 event → Lambda → Step Functions → SageMaker Pipelines.

**相关题目**: Q101(运营-并行), Q98(审批), Q61, Q102(实施-MLOps), Q85(会话), Q95(实施-混合路由), Q100(UGC 编排)
**参考**: https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html

---

### KP11. 向量数据库选型 ⭐⭐⭐⭐
### Vector Store Selection

**中文详解**（对照表直接背）：
| 场景 Scenario | 选型 Pick |
|---|---|
| 托管 RAG、最少开发量 | **Knowledge Bases**（帮你选底层向量库） |
| 大规模向量 + 元数据过滤 + 混合搜索、免集群管理 | **OpenSearch Serverless** |
| <100 万向量、向量+元数据同库、自动扩缩 | **Aurora PostgreSQL Serverless + pgvector** |
| 小型专有数据集、极致召回精度、微秒延迟 | **MemoryDB + HNSW 索引**（小数据集 AWS 推荐 HNSW；Flat 暴力开销大） |
| 多跳实体关系 GraphRAG | **Neptune Analytics** |
| 高吞吐语义搜索（非 RAG 抽象层） | OpenSearch Service + k-NN |
- **混合搜索 hybrid search**（向量 + BM25 关键词）：治"漏精确术语/缩写/法条引用"——OpenSearch 原生支持，同索引融合排序，常配合 reranker。

**English Deep Dive**: Managed RAG → **Knowledge Bases**; large-scale vectors + metadata filters + hybrid search, no cluster management → **OpenSearch Serverless**; <1M vectors co-located with metadata, auto-scaling → **Aurora Serverless + pgvector**; small proprietary datasets needing max recall at microsecond latency → **MemoryDB + HNSW** (AWS-recommended for small datasets; Flat is brute-force); multi-hop GraphRAG → **Neptune Analytics**; high-throughput semantic search → OpenSearch k-NN. **Hybrid search** (vector + BM25) fixes missed exact terms/acronyms/citations — natively supported in OpenSearch, often paired with a reranker.

**相关题目**: Q63, Q59, Q68(领域1), Q107(领域1), Q89(法律), Q90(GraphRAG), Q78(餐厅), Q81
**参考**: https://docs.aws.amazon.com/opensearch-service/latest/developerguide/knn.html

---

### KP12. 模型评估与质量门禁 ⭐⭐⭐⭐
### Model Evaluation & Quality Gates

**中文详解**：
- **Bedrock Model Evaluation 作业**：输入 = **JSONL 数据集放 S3**，输出到另一 S3 位置；**judge 模型固定不变**（LLM-as-a-judge，如 Claude Sonnet）评所有候选 FM（candidates 是 generator 不是 evaluator！），保证 apples-to-apples；数值评分（1–5）支持自动化阈值门禁。
- **retrieve-only vs retrieve-and-generate**：前者只评检索；横向比较 chunking 策略对**生成质量**的影响必须用后者 + 同一评估数据集并列多策略。
- **CI/CD 质量门禁**：评估指标阈值不达标就阻断部署（CodePipeline 触发评估）；多语言一致性用**语义等价测试集 + 相似度阈值**。
- **A2I (Augmented AI)**：按置信度/规则把高风险交互路由人工——"自动评估规模化 + 定向人工复审"组合拳；也是 Textract 低置信抽取转人工的服务。
- prompt 版本历史 + 量化指标 + 自动化回归测试 = 诊断输出不一致三件套。

**English Deep Dive**: **Evaluation jobs**: JSONL datasets in S3, results to S3; keep the **judge model fixed** (LLM-as-a-judge) across all candidate FMs (candidates are generators, not evaluators!) for apples-to-apples; numeric scores enable automated gates. **retrieve-only** assesses retrieval; comparing chunking strategies' effect on generation requires **retrieve-and-generate** with strategies side-by-side in one dataset. **CI/CD quality gates** block deploys below thresholds; cross-language consistency needs **semantically equivalent multilingual test sets + similarity thresholds**. **A2I** routes high-risk items to humans by confidence — the "automated at scale + targeted human review" combo; also the human-review service for low-confidence Textract extractions. Prompt version history + quantitative metrics + automated regression tests = the inconsistency-diagnosis trio.

**相关题目**: Q53, Q39, Q105(测试)×2, Q90(测试), Q95(测试), Q99(测试-电商), Q101(测试)×2, Q84(测试)
**参考**: https://docs.aws.amazon.com/bedrock/latest/userguide/model-evaluation.html

---

### KP13. 安全治理：IAM / SCP / 私有访问 / 数据湖授权 ⭐⭐⭐⭐
### Security Governance: IAM, SCPs, Private Access, Lake Formation

**中文详解**：
- **IAM ABAC**（按属性/部门标签授权）：大规模控制 FM 访问模式。
- **IAM 条件键**：强制 `GuardrailIdentifier` / `ModelId` ——"只能用获批模型和护栏"。
- **SCP**：Organizations 最强边界，优先于一切 IAM 授权；可校验请求参数（权限边界不行）。
- **VPC endpoint (PrivateLink)**：私有调 Bedrock 唯一正解；IAM 条件键 `aws:sourceVpce` 锁定批准端点。
- **Lake Formation + LF-Tags**：数据湖**列级**、跨账号授权的唯一解（S3 bucket policy/IAM 做不到列级）；FM 只访问脱敏列子集。
- **Macie**：静态数据 PII/敏感数据自动发现分类（不是实时过滤！）。
- **Glue Data Catalog**：数据源注册 + 元数据 + **血缘追踪**——"端到端可追溯/归因"答案。
- **联合认证**：Entra ID ↔ IAM 用 **SAML 联合**；按部门建 IAM 角色限定 ModelId。
- **S3 审计体系**：对象元数据 + 标签 + server access logging + Object Lock（不可篡改留存）。

**English Deep Dive**: **IAM ABAC** for attribute/department-based FM access at scale; **IAM condition keys** to enforce `GuardrailIdentifier`/`ModelId` ("approved models and guardrails only"); **SCPs** as the strongest boundary, evaluated before IAM, capable of validating request parameters (permission boundaries can't); **VPC endpoints (PrivateLink)** as the only correct private-access answer, lockable via `aws:sourceVpce`; **Lake Formation LF-Tags** as the only column-level, cross-account data-lake authorization (bucket policies can't do columns); **Macie** for at-rest sensitive-data discovery (not real-time filtering!); **Glue Data Catalog** for source registration + lineage; **SAML federation** for Entra ID↔IAM; S3 object metadata + tags + access logging + Object Lock for tamper-proof audit.

**相关题目**: Q80(AI安全), Q93(AI安全), Q90(AI安全-SCP), Q100(Entra), Q86×2, Q78(实施-私有), Q95(三洲驻留), Q107(血缘), Q68(AI安全-IaC)
**参考**: https://docs.aws.amazon.com/bedrock/latest/userguide/security.html ｜ https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html

---

## 🥉 TIER 3 — 常见考点 / Common Topics

---

### KP14. 数据预处理与专用 AI 服务分工 ⭐⭐⭐
### Data Preprocessing & Purpose-Built AI Services

**中文详解**（服务分工表，选错服务是主要陷阱）：
| 任务 Task | 服务 Service |
|---|---|
| 大规模数据准备/编目/质量校验 | **Glue**（crawler 推断 schema 入 Data Catalog、ETL 自定义分块、Data Quality 内置规则+指标） |
| 文本 PII 检测与脱敏 | **Comprehend**（还有 toxicity detection、prompt safety classification） |
| 扫描件/表单结构化抽取 + 置信度 | **Textract**（低置信 → A2I 人工） |
| 语音转文字 | **Transcribe**（streaming + partial results 实时；batch 非实时） |
| 图像/视频内容审核 | **Rekognition**（image moderation） |
| 大规模多模态文档处理编排 | **Bedrock Data Automation (BDA)** |
| 静态数据敏感发现 | **Macie** |
- 注意：并行执行多个 Comprehend 检测避免串行延迟；PII 必须 **redaction** 不能仅 detection。

**English Deep Dive**: Glue for large-scale prep/catalog/quality (crawlers → Data Catalog, ETL chunking, Data Quality rules); Comprehend for PII detection/redaction + toxicity + prompt-safety classification; Textract for structured extraction from scans (low confidence → A2I); Transcribe for speech (streaming+partial for real-time); Rekognition for image/video moderation; **Bedrock Data Automation** for multimodal processing at scale; Macie for at-rest discovery. Run Comprehend checks **in parallel** to avoid serial latency; PII needs **redaction**, not just detection.

**相关题目**: Q95(Glue), Q55, Q79(BDA), Q87(贷款), Q87(Comprehend 分层), Q100(UGC), Q99(野生动物)
**参考**: https://docs.aws.amazon.com/comprehend/latest/dg/what-is.html

---

### KP15. 韧性设计：重试、熔断、金丝雀 ⭐⭐⭐
### Resilience: Retries, Circuit Breakers, Canary Releases

**中文详解**：
- **重试三件套**：指数退避 + **jitter**（打散重试防同步风暴）+ **熔断器**（错误率超阈值暂停重试，防压垮服务/级联故障）。固定延迟固定次数 = 干扰项。
- **流式恢复**：缓冲已收 chunk，从断点续传，不整流重启。
- **金丝雀发布**：EventBridge 触发 → Step Functions 分阶段切流（stage/wait/条件分支）→ Lambda 查 CloudWatch 指标 → 健康增量、恶化**自动回滚**，零人工。
- **IaC 治理**：CloudFormation 模板 + 集中版本仓库 + **CloudFormation Guard 在 CI/CD 预部署阶段**强制合规（部署后才检查 = 干扰项）。

**English Deep Dive**: Retry trio: exponential backoff + **jitter** + **circuit breaker** (pause retries above an error threshold — prevents cascading failure). Fixed-delay retries are distractors. **Streaming recovery**: buffer received chunks, resume from last position. **Canary releases**: EventBridge → Step Functions staged traffic shifting → Lambda checks CloudWatch metrics → increment if healthy, **auto-rollback** if not, zero human touch. **IaC governance**: CloudFormation + central repo + **CloudFormation Guard in the CI/CD pre-deploy phase** (post-deploy checks are distractors).

**相关题目**: Q84(运营), Q103(运营), Q106(运营-金丝雀), Q68(AI安全-Guard)
**参考**: https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html

---

### KP16. Bedrock Flows 与 Prompt Management ⭐⭐⭐
### Bedrock Flows & Prompt Management

**中文详解**：
- **Prompt Management**：prompt **版本化 + 审批工作流 + 变量参数化**（必填输入定义为变量、指定输出格式）——"数百模板跨团队治理/版本控制+通知审批+审计"的答案；配合 IAM 控审批权限 + CloudTrail 审计。也用于保证同输入同 prompt 版本 → 输出一致性。
- **Bedrock Flows**：可视化托管编排多步 GenAI 工作流（S3 action 节点取/存数据、expression 节点确定性解析、prompt/agent 节点挂 guardrail）——比 agent+Lambda 免胶水代码。flow 的 prompt 节点可直接挂 Prompt Management 模板和 guardrail。
- **Intelligent prompt routing**：按 prompt 复杂度自动路由到大小模型——"70% 简单问题省钱 + 30% 复杂问题保质量、最少实现工作量"的原生解。

**English Deep Dive**: **Prompt Management**: prompt **versioning + approval workflows + variable parameterization** (required inputs as variables, output format specification) — the answer for "hundreds of templates across teams, version control + approval + audit"; enforces same-prompt-version reproducibility. **Bedrock Flows**: managed visual orchestration (S3 action nodes, expression nodes for deterministic parsing, prompt/agent nodes with guardrails) — less glue code than agent+Lambda. **Intelligent prompt routing**: auto-routes prompts to right-sized models by complexity — the native answer for "70% simple queries cheaper, 30% complex queries high quality, least effort."

**相关题目**: Q107(Prompt Mgmt), Q102(Flow-资助), Q76, Q80(医疗-Flows), Q89(审核流), Q89(路由)
**参考**: https://docs.aws.amazon.com/bedrock/latest/userguide/flows.html ｜ https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-management.html ｜ https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-routing.html

---

### KP17. 成本与延迟优化 ⭐⭐⭐
### Cost & Latency Optimization

**中文详解**：
- **Token 治理**：Bedrock 只在调用后暴露 token 用量 → 主动额度管控需在 Lambda 内置**模型专用 tokenizer 调用前估算** + CloudWatch 告警 + DynamoDB 明细做成本分摊。
- **降延迟**：`PerformanceConfigLatency=optimized`（免新增基础设施降推理延迟）；token batching 合并请求降开销；并行化独立调用；**交互式请求绝不选 batch inference**（离线异步）。
- **缓存陷阱**：交互大多为唯一 → 缓存命中率低 → 别选缓存方案（考 2 次）。
- **SageMaker 实时端点**：首 token 慢 → 最小实例数 >0 消灭冷启动 + 响应流式 + 容器启动预加载模型 + 动态批处理；**异步推理不适合交互聊天**。

**English Deep Dive**: **Token governance**: Bedrock exposes token usage only after calls → proactive control needs model-specific **tokenizers in Lambda for pre-call estimation** + CloudWatch alarms + DynamoDB chargeback records. **Latency**: `PerformanceConfigLatency=optimized`; token batching; parallelize independent calls; **never pick batch inference for interactive requests**. **Cache trap**: if interactions are mostly unique, caches have low hit rates — don't pick caching (asked 2×). **SageMaker real-time endpoints**: min instances > 0 (kill cold starts) + response streaming + model preloading + dynamic batching; async inference is not for chat.

**相关题目**: Q100(token), Q99/96(缓存), Q105(SageMaker 延迟), Q105(token batching), Q91
**参考**: https://docs.aws.amazon.com/bedrock/latest/userguide/inference-parameters.html

---

### KP18. 周边服务速记 ⭐⭐⭐
### Peripheral Services Quick Notes

**中文详解**：
- **Amazon Q Business**：企业助手；S3 数据源用**单一 acl.json**（前缀→Identity Center 组映射）做文档级 ACL；**data accessor** 机制让数据留在客户侧、授权第三方受控可审计访问。
- **Amazon Q Developer customization**：让代码建议基于组织内部代码库/规范——"强制新团队用批准库且不做项目级修改"的答案（项目内 rules 文件夹都是干扰项）。
- **Amplify AI Kit**：AppSync/Amplify 架构内实现 GraphQL 流式响应的最低摩擦方案（治 RequestResponse 同步超时）。
- **SageMaker Neo**：编译模型到目标硬件——"云+本地混合部署/边缘推理"信号。
- **Converse API vs Kendra**：Kendra 偏企业搜索/FAQ，不是对话推荐 RAG——常作干扰项。

**English Deep Dive**: **Q Business**: single **acl.json** (prefix→Identity Center group mapping) for document-level ACLs on S3 data sources; the **data accessor** mechanism keeps data customer-side with controlled, auditable third-party access. **Q Developer customization** grounds code suggestions in your internal codebase — the answer for "enforce approved libraries with no project-level changes." **Amplify AI Kit**: lowest-friction streaming for AppSync/Amplify GraphQL. **SageMaker Neo** compiles models for edge/on-prem targets. **Kendra** is enterprise search/FAQ, not conversational RAG — a frequent distractor.

**相关题目**: Q99(Q Business ACL), Q97(data accessor), Q82(Q Developer), Q68(Amplify), Q93(Neo)
**参考**: https://docs.aws.amazon.com/amazonq/latest/qbusiness-ug/what-is.html

---

# 第四部分：107 题速查表（按领域分组）
# Part 4: Question Quick-Reference (Grouped by Domain)

> 用法：读"场景信号"一眼认题 → 直接锁定答案。⚠️ 注意题库 PDF 本身存在重复题号（如 Q104/Q106 多次出现），请按"场景信号"而非题号认题。
> Usage: recognize the question by its "signal" and lock in the answer. Note the source PDF has duplicate question numbers — match by scenario, not by number.

## 领域 1：基础模型集成、数据管理与合规 / Domain 1 (27 题)

| 场景信号 Signal | 答案 | 速记要点 Key Point |
|---|---|---|
| 数据主张链接到源文档 + 托管 + <3s | **A** | KB source attribution + 高相关性阈值 + S3 审计 |
| 每酒店数据隔离 + 近实时 | **C** | 每租户一个 KB + 直接摄取/定期同步分层 |
| 万级多模态文档（PDF/视频）+ 协作版本 | **B** | BDA + Textract/Transcribe + S3 版本 + AppSync subscriptions |
| 固定 500 token 分块切断法律论证 | **C** | 语义边界分块 + 重新生成嵌入 |
| S3 金融数据预处理（质量/元数据/分块） | **B** | Glue crawler + ETL + Data Quality 一站式 |
| 小型专有向量库极致准确 | **B** | MemoryDB + HNSW + 垂直扩展 |
| 漏精确医学术语/缩写 | **A** | OpenSearch hybrid search（向量+BM25） |
| 50GB JSON 去 PII 入向量库、4 小时、成本最优 | **D** | Step Functions + Comprehend + Bedrock 嵌入 + OpenSearch Serverless |
| 跨"方法/结果/讨论"章节引用（考 2 次） | **B** | hierarchical chunking：父 1000 / 子 200 / 重叠 50 |
| 图书推荐、1 万+ 并发 | **A** | KB + OpenSearch + Lambda + API Gateway |
| 无关键词、按含义匹配、<100 万文件 | **D** | Titan Embeddings + Aurora pgvector Serverless |
| 千万级多语文档 + 元数据过滤 | **A** | OpenSearch Serverless + KB 集成 |
| 投资工具↔板块↔监管多跳关系 <3s | **A** | KB GraphRAG + Neptune Analytics |
| 欧美低延迟 + 欧洲数据不出境 + 峰值配额 | **B** | 地理代码 CRI profile + 分区 API Gateway |
| 输出完全可追溯（注册/归因/审计） | **D** | Glue Data Catalog 标签归因 + CloudTrail |
| 财报表格↔解释文字关联 | **B** | KB 按文档结构上下文分块 + 引用 |
| 展示推理过程 + 引用 + 1 万并发 2 秒 | **A** | Agents tracing + KB + API GW/Lambda/CloudFront |
| S3 医学论文一致元数据框架 | **A** | 系统元数据(时间)/用户元数据(作者)/对象标签(分类) |
| 临床建议只能基于获批文档 + 引用 | **B** | KB + RetrieveAndGenerate（原生引用） |
| 5000 万月活语义搜索、最少开发量 | **D** | 迁入 KB，Retrieve API 直接自然语言查询 |
| 餐厅语义搜索、95%<500ms、小时级新鲜 | **B** | OpenSearch k-NN + 同一 Bedrock FM 嵌入文档与查询 |
| Q Business 按部门组控访问 | **B** | 单一 acl.json（前缀→Identity Center 组） |
| 客户保留数据所有权 + 第三方受控检索 | **A** | Q Business data accessor 机制 |
| 法律检索：语义 + 法条精确匹配 | **B** | OpenSearch hybrid search + Bedrock reranker |
| 外科医生看 PII / 工程师脱敏 / 只引 3 年内报告 | **C** | 推理时按 Cognito 组选 guardrail + S3 Lifecycle |
| 欺诈检测：PII 不进生产 + 列级授权 | **B** | IAM 角色 + Lake Formation LF-Tag + CloudTrail |
| Bedrock Flow 必填输入 + 霸凌只通知不阻断（选 2） | **AD** | Prompt Mgmt 变量/格式 + insults 过滤器 detect 模式 |

## 领域 2：实施与集成 / Domain 2 (21 题)

| 场景信号 Signal | 答案 | 速记要点 Key Point |
|---|---|---|
| 呼叫中心 <1s、边说边转写、双向流式 | **B** | Transcribe streaming partial + InvokeModelWithResponseStream + WebSocket |
| 多 FM 动态路由、规则小时级变、不部署代码（考 2 次） | **C** | Lambda + AppConfig Agent 拉配置 |
| S3 上传自动重训练重部署 | **D** | 事件 → Lambda → Step Functions Standard → SageMaker Pipelines |
| 托管 FM API：流式 + token 上限 + 重试 | **A** | HTTP API + Lambda response streaming，上限在服务端强制 |
| 混合部署（云+本地）+ 高吞吐批处理（选 2） | **BC** | Provisioned Throughput + SageMaker Neo 边缘编译 |
| 逐字符实时展示、数千并发、15–45s 响应 | **A** | WebSocket API + Lambda + 流式 API（禁前端直连持凭证） |
| agent 跨交互记忆 + 双调用模式（选 2） | **AB** | AgentCore + API Gateway/EventBridge 注册 actions |
| 澄清工作流 + 会话加密存储、数千并发 | **B** | Step Functions Standard（回调等待）+ DynamoDB on-demand+SSE |
| 多部门多智能体 + 数据隔离 + 易扩展（考 2 次） | **A** | supervisor 路由 + collaborator + 每部门 KB/IAM |
| 跨 Lambda/EKS 多轮对话同一 FM | **D** | Converse API + messages 数组 + IAM 角色 |
| 电商幻觉出不存在商品 + 交互唯一（考 2 次） | **C** | KB RAG 接地 + PerformanceConfigLatency=optimized |
| Step Functions 中间输出超 256KB | **B** | 大对象存 S3，ResultPath/ResultSelector 只传引用 |
| 强制团队用批准内部库、不改项目 | **D** | Amazon Q Developer customization |
| 简单扩展名路由 + 复杂语义路由 + 执行历史 | **B** | 简单路由写代码内 + 复杂路由 Step Functions（JSONata） |
| 审核外发消息：S3 取→抽取部分→送模型→写回 | **A** | Bedrock Flows（S3 节点 + expression 解析 + agent 步骤） |
| 密钥过期/模板过期级联故障 | **A** | Map 状态并行 + Lambda 传最新元数据 + AppConfig 版本门控 |
| 运行时切换模型提供商、安全发布可回滚 | **B** | AppConfig 存当前模型，Lambda 运行时读取 |
| Lambda 私有子网纯私有调 Bedrock + 跨账号列级授权 | **A** | interface VPC endpoint + Lake Formation LF-tag（⚠️原答案键标 B，解析实为 A） |

## 领域 3：AI 安全、保障与治理 / Domain 3 (24 题)

| 场景信号 Signal | 答案 | 速记要点 Key Point |
|---|---|---|
| UGC 图文审核 + PII + 接入 S3 工作流 | **D** | Step Functions 编排 Guardrails + Comprehend PII + Rekognition |
| 全组织禁止特定主题 + 只用获批模型（选 2） | **CD** | SCP 强制 guardrail ID + StackSets 统一下发 block 策略 |
| SCP 限欧洲两区、FM 在他区、私有访问 | **C** | EU 范围 CRI profile + VPC endpoint + SCP 放行 profile |
| 金融客服：禁投资建议/PII/低误报/差异化动作 | **C** | 单一 guardrail：medium + denied topics + 输入 block/输出 mask |
| 医疗端到端血缘 + 实时 PII + 自动合规报告 | **A** | Glue Catalog + Guardrails PII + CloudTrail + Macie |
| 数百 prompt 模板：版本+审批+审计+参数化 | **B** | Bedrock Prompt Management + IAM 审批权限 + CloudTrail |
| 防提示注入 + 干预审计 + 跨区域一致 | **A** | 内容过滤 high + guardrail profile + CloudWatch Logs 干预事件 |
| 视频平台：控 FM 访问模式 + 完整审计 | **B** | ABAC + 条件键锁 ModelId/Guardrail + VPC endpoint + CloudTrail Lake |
| SCP 拒绝 Nova Pro CRI 调用（选 2） | **BE** | SCP 放行 `eu.amazon.nova-pro-v1:0` / `eu-*` profile |
| 金融三件事：阻断高风险/禁竞争对手/有据可依（选 3） | **ADF** | denied topics + 自定义词过滤 + 高 grounding 阈值 |
| 银行 PII 双向过滤 + 审计含图片 | **C** | Guardrails 敏感信息+话题策略 + Converse + S3 delivery/image logging |
| 99.5% 一致性 + <1s + 拦截危险建议 | **A** | Provisioned Throughput + Guardrails + Prompt Mgmt 审批 |
| 银行贷款：扫描抽取 + 低置信转人工 + 驻留（选 3） | **ABD** | Textract+A2I / 推理前 PII 脱敏+Guardrails / S3 元数据+区域化 IAM |
| MCP server 只授权用户访问 | **C** | Lambda+API GW 托管 + Streamable HTTP + Cognito OAuth 2.1 |
| 金融 60 天上线 + <200ms + 幻觉监控 | **A** | Guardrails + Model Evaluation + DynamoDB TTL + CloudWatch |
| 法规要求 AI 建议必须人工审批 | **B** | Step Functions waitForTaskToken + DynamoDB 审批记录 |
| 上线前评估 PII 防泄露 + 数据驻留 | **B** | 测试 mask / 生产 block / 每 Region 各部署 guardrail |
| 三洲数据驻留 + 审计 + 数据分类 | **C** | S3 Object Lock+区域桶策略 + Macie 分类 + CloudTrail |
| 两个人口群体公平性实时监测 >15% 告警 | **B** | Prompt Mgmt 变体 + Flows 分流 + guardrails 指标 + InvocationsIntervened 告警 |
| FM 只访问脱敏列子集 + 全量审计 | **B** | Lake Formation LF-Tag 授权 + CloudTrail |
| Comprehend 三层过滤且全部在 FM 前完成 | **D** | 并行 toxicity + prompt safety + PII redaction |
| 动物研究多模态 + 防推测性解读 + 伦理审计 | **B** | 预处理摘要入 KB + guardrails 限推测 + AppConfig + CloudTrail |
| Entra ID 认证 + 私有网络 + 按部门限模型 | **A** | SAML 联合 + IAM 角色限 ModelId + PrivateLink + 双层审计 |
| 5 业务单元标准化组件 + 部署前强制合规 | **B** | CloudFormation 模板库 + Guard 在 CI/CD 预部署拦截 |

## 领域 4：运营效率与优化 / Domain 4 (22 题)

| 场景信号 Signal | 答案 | 速记要点 Key Point |
|---|---|---|
| RAG 检索语义相似但无关、最少运维 | **D** | KB 内置 reranking 配置（优于调 Rerank API） |
| 买了 PT 仍按需限流、容量闲置（考 2 次） | **B** | modelId 换成 provisioned 模型 ARN |
| 主动 token 额度预警 + 5000 请求/分 + 成本分摊 | **A** | Lambda 内置 tokenizer 调用前估算 + CloudWatch + DynamoDB |
| 70% 简单 / 30% 复杂问题省钱路由 | **B** | Bedrock intelligent prompt routing |
| token 消耗突增定位到工具、阈值自适应 | **C** | invocation logs + metric filters + anomaly detection |
| 间歇超时 + 流断裂 + 防压垮服务 | **B** | 指数退避+jitter + 熔断器 + 断点续流 |
| 峰值 3 倍、多 Region、<2s | **B** | token batching + cross-Region inference profiles |
| 医疗问答语义稀释 + 术语消歧 | **B** | KB query decomposition + Bedrock Flows 编排 |
| 50 万并发 <200ms + 月度预算 | **B** | 低延迟模型 + PT + 自动扩缩 |
| 串行 45s 压到 10s | **B** | Step Functions Parallel 并行独立调用 |
| token 可视化 + 多角色仪表盘（选 2） | **CD** | CloudWatch 原生指标 + Managed Grafana zero-ETL |
| 金丝雀发布自动切流自动回滚 | **A** | EventBridge → Step Functions 分阶段 + Lambda 查 CloudWatch |
| 基线退化 + 10 分钟关联告警（考 2 次） | **C** | Application Insights + EMF 维度指标 + anomaly detection + Logs Insights |
| 幻觉 + token 异常 + 成本预警近实时 | **C** | 调用日志存 S3 + grounding check + token anomaly detection |
| 全球时区限流 + 低谷无固定费用 | **C** | CRI 分散流量（on-demand），监控 InvocationThrottles |
| 瞬态错误 + 级联防护 + 按模型关联延迟 | **B** | SDK 标准重试 + X-Ray 注解（model ID/参数/Region） |
| 欧美驻留 + 峰值配额降级（考 2 次） | **B** | 地理代码 CRI profile + 分区 API Gateway 端点 |
| 按客户等级切模型 + A/B + 参数校验 | **C** | AppConfig feature flags + JSON schema + Agent 缓存 |
| SageMaker 首 token 2s 放弃、p95<800ms（选 2） | **AD** | 预热+动态批处理；min instances>0 + 响应流式 |
| 多 Region prompt 链不一致 + 危险建议 | **A** | PT 稳延迟 + Guardrails 拦截 + Prompt Mgmt 版本审批 |

## 领域 5：测试、验证与故障排除 / Domain 5 (11 题)

| 场景信号 Signal | 答案 | 速记要点 Key Point |
|---|---|---|
| 横向比 chunking 策略 + 评两个 FM + 质量阈值 | **B** | retrieve-and-generate 评估 + 固定同一 judge + 数值评分门禁 |
| 样例提示自动生成各 FM 评估报告 | **B** | JSONL 放 S3 + Bedrock 评估作业；judge 固定，候选 FM 是 generator |
| AppSync 同步调用 RAG 频繁超时 | **A** | Amplify AI Kit 流式响应（最小改动） |
| 自动规模评估 + 定向人工复审 | **B** | Evaluations（LLM judge）+ Guardrails + A2I |
| 多语言不一致 + 45 分钟 1.5 万会话 + CI/CD 门禁 | **D** | 语义等价多语测试集 + 评估作业并行 + 相似度/幻觉阈值 |
| 复杂文档输出不一致：诊断+对比+版本史 | **B** | prompt 版本控制 + 量化指标测试套件 + 自动化回归 |
| 50–200 页文档超窗口截断 | **C** | 语义分块（percentile 95%）+ RetrieveAndGenerate 动态检索 |
| 医疗 RAG：检索准确率 + 幻觉 + 降人工成本 | **D** | LLM-as-a-judge 初筛 + 边缘案例人工 + 内置 RAG 指标 |
| 电商内部评估平台上 CloudWatch（选 2） | **BC** | S3 数据集+IAM/VPC endpoint；Lambda 定时建评估作业发指标 |
| prompt 模板 QA + 不达标禁部署 | **B** | evaluation jobs + CodePipeline 触发 + 阈值门禁 |
| KB 检索过宽（类型/时间）、嵌入已最优 | **C** | metadata-aware filtering 收窄候选集 |

---

# 第五部分：资深专家补充 —— 题库之外你必须掌握的考点
# Part 5: Expert Supplements — Beyond the 107 Questions

> 以下内容来自资深 AWS GenAI 架构师视角：题库 107 题覆盖了大部分考点，但官方考纲中还有这些"隐性重点"，考场上遇到不至于陌生。
> From a senior AWS GenAI architect's perspective: the bank covers most topics, but these "hidden weights" from the official syllabus may still appear.

## S1. 推理参数与输出确定性 / Inference Parameters & Determinism ⭐必补

**中文**：
- **temperature**（0–1）：越低越确定。要"一致输出/可复现"→ temperature 设低（0–0.2）；要"创意"→ 调高。幻觉场景**绝不能**建议升 temperature（反向陷阱）。
- **top-p / top-k**：核采样与候选截断，与 temperature 配合控制多样性。
- **maxTokens**：输出上限；配合 stopSequences 控制结构化输出。
- 可复现性组合拳 = 固定 prompt 版本 + 固定推理参数 + provisioned 容量。
- **Converse API 的 inferenceConfig / additionalModelRequestFields**：统一传参口。

**English**: **temperature** — lower = more deterministic (0–0.2 for reproducibility; never raise it to fight hallucination — a reverse trap). **top-p/top-k** — nucleus sampling and candidate truncation. **maxTokens** + **stopSequences** for structured output. Reproducibility = fixed prompt version + fixed inference parameters + provisioned capacity. Use Converse's `inferenceConfig` / `additionalModelRequestFields`.

📖 https://docs.aws.amazon.com/bedrock/latest/userguide/inference-parameters.html

## S2. RAG vs 微调 vs 继续预训练 决策矩阵 / RAG vs Fine-Tuning vs Continued Pre-Training ⭐必补

**中文**（决策题高频）：
| 需求 Need | 选择 Choice |
|---|---|
| 知识新鲜、需引用、数据常变 | **RAG**（首选，成本低见效快） |
| 改变模型行为/风格/格式、领域术语对齐 | **Fine-tuning**（需标注数据集，PT 托管运行） |
| 注入大量领域知识（语料级） | **Continued Pre-training**（未标注语料） |
| 自带训练好的模型上 Bedrock | **Custom Model Import** |
- 口诀：**先 RAG，再 prompt，最后才微调**。微调模型只能用 Provisioned Throughput 调用。

**English**: Fresh knowledge + citations → **RAG first**; change model behavior/style/format → **fine-tuning** (labeled data, invoked via Provisioned Throughput); inject corpus-level domain knowledge → **continued pre-training** (unlabeled corpus); bring your own trained model → **Custom Model Import**. Mantra: RAG first, prompt engineering second, fine-tune last.

📖 https://docs.aws.amazon.com/bedrock/latest/userguide/custom-models.html

## S3. Bedrock Agents 深入 / Bedrock Agents Deep Dive

**中文**：
- **Action Groups**：agent 可调用的工具，用 **OpenAPI schema** 定义 API 或 Lambda 函数。
- **Return of Control (RoC)**：agent 把参数返回给调用方、由调用方自己执行——适合已有后端系统/不便暴露 Lambda 的场景。
- **记忆与上下文**：会话内记忆默认开启；**memory（跨会话长期记忆）** 可配保留天数。
- **Code Interpretation**：agent 生成并运行代码做数据分析/图表。
- **编排原理**：ReAct 循环（推理→行动→观察）；trace 可看每步。

**English**: **Action Groups** define tools via **OpenAPI schemas** or Lambda; **Return of Control** hands parameters back to the caller to execute (fits existing backends); session memory is on by default, long-term **memory** has configurable retention; **Code Interpretation** lets agents run generated code; orchestration follows a ReAct loop visible in traces.

📖 https://docs.aws.amazon.com/bedrock/latest/userguide/agents.html

## S4. Prompt Engineering 技术清单 / Prompt Engineering Checklist

**中文**：zero-shot / few-shot（给示例）/ chain-of-thought（逐步推理，注意增加 token 与延迟）/ system prompts（设定角色与边界）/ Claude 的 XML 标签结构化输入 / 指令放在数据前还是后（Claude 推荐长文档放前、问题放后）/ 负面指令 vs 正面指令（说"要做什么"优于"不要做什么"）。考试考的是**权衡**：CoT 提准确率但增延迟成本——高吞吐场景是干扰项。

**English**: zero-shot / few-shot / chain-of-thought (better reasoning but more tokens and latency — a distractor in high-throughput scenarios) / system prompts / XML tags for Claude / instruction placement (long docs first, question last for Claude) / positive over negative instructions. The exam tests **trade-offs**, not definitions.

📖 https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-engineering-guidelines.html

## S5. 负责任 AI 与合规框架 / Responsible AI & Compliance Frameworks

**中文**：AWS Responsible AI 六维度：公平性 fairness、可解释性 explainability、隐私 privacy、安全 security、稳健性 robustness、治理 governance。工具映射：公平性→SageMaker Clarify（离线偏差分析，**不是**实时监控）；幻觉→Guardrails grounding；隐私→PII 过滤+KMS 加密；审计→CloudTrail+invocation logging。合规框架词：GDPR（数据驻留/被遗忘权）、HIPAA、SOC、ISO/IEC 42001（AI 管理体系）。**AI 生成内容溯源**：数字水印与内容凭证（C2PA）。

**English**: AWS Responsible AI dimensions: fairness, explainability, privacy, security, robustness, governance. Tool mapping: fairness → SageMaker Clarify (offline bias analysis, **not** real-time); hallucination → Guardrails grounding; privacy → PII filters + KMS; audit → CloudTrail + invocation logging. Frameworks: GDPR (residency/erasure), HIPAA, SOC, ISO/IEC 42001. Provenance: watermarking and content credentials (C2PA).

📖 https://docs.aws.amazon.com/bedrock/latest/userguide/responsible-use.html

## S6. 数据安全与加密 / Data Security & Encryption

**中文**：静态加密用 **KMS**（S3、DynamoDB、CloudWatch Logs、KB 向量库均可 CMK）；传输中 TLS；**Bedrock 不会用客户数据训练基础模型**（重要承诺，可能直接考）；VPC endpoint 让推理流量不出私网；删除数据时注意 KB 需重新同步（StartIngestionJob）；S3 Object Lock 做 WORM 留存。

**English**: Encrypt at rest with **KMS** (S3, DynamoDB, logs, KB vector stores support CMKs); TLS in transit; **Bedrock never trains base FMs on customer data** (a directly testable commitment); VPC endpoints keep inference private; re-sync KBs (StartIngestionJob) after source changes; S3 Object Lock for WORM retention.

📖 https://docs.aws.amazon.com/bedrock/latest/userguide/data-protection.html

## S7. 配额、吞吐与成本计算 / Quotas, Throughput & Cost Math

**中文**：On-demand 有账户级 TPS/TPM 配额（Service Quotas 可申请提升）；PT 按 **Model Unit 小时**计费，1 MU 的吞吐因模型而异；估算公式：峰值 TPS × 每请求 token × 安全边际 → MU 数。**Batch inference**：离线异步、结果写 S3、价格更低——"非实时大批量"信号。成本优化顺序：路由到小模型 → 缩短 prompt/上下文 → 缓存（仅高重复场景）→ batch → PT 承诺折扣。

**English**: On-demand has account-level TPS/TPM quotas (raise via Service Quotas); PT bills per **Model Unit-hour**, MU throughput varies by model; sizing: peak TPS × tokens/request × safety margin → MUs. **Batch inference**: async, results to S3, cheaper — the "non-real-time bulk" signal. Cost optimization order: route to smaller models → shorten prompts/context → cache (only high-repetition) → batch → PT commitment discounts.

📖 https://docs.aws.amazon.com/bedrock/latest/userguide/batch-inference.html ｜ https://docs.aws.amazon.com/bedrock/latest/userguide/quotas.html

## S8. RAG 评估指标 / RAG Evaluation Metrics

**中文**：检索侧：context precision（检准）、context recall（检全）、precision@k；生成侧：faithfulness（忠于上下文，幻觉反面）、answer relevancy、correctness。开源框架 **RAGAS** 常被提及（自建环境，考试答案更偏 Bedrock 托管评估）。LLM-as-a-judge 评分尺度要数值化（1–5）才能做门禁。

**English**: Retrieval: context precision/recall, precision@k. Generation: faithfulness (anti-hallucination), answer relevancy, correctness. Open-source **RAGAS** is often referenced (self-managed; exam answers favor managed Bedrock evaluation). Judge scores must be numeric (1–5) to serve as gates.

📖 https://docs.aws.amazon.com/bedrock/latest/userguide/model-evaluation.html

## S9. 多模态与嵌入细节 / Multimodality & Embedding Details

**中文**：**Titan Multimodal Embeddings** 支持图文联合向量（以文搜图）；Titan Text Embeddings V2 支持**维度裁剪**（1024/512/256）降存储成本；嵌入维度越高越准但越贵越慢（题目里"升维度解决质量问题"几乎都是干扰项）。图像理解用 Claude/Nova 多模态模型直接读图；视频/音频先 BDA/Transcribe 转文本再入 RAG。

**English**: **Titan Multimodal Embeddings** for joint image-text vectors (text-to-image search); Titan Text Embeddings V2 supports **dimension truncation** (1024/512/256) to cut storage cost; higher dimensions = more accurate but slower/pricier ("raise dimensions to fix quality" is almost always a distractor). Use Claude/Nova multimodal models for direct image understanding; transcode video/audio to text (BDA/Transcribe) before RAG.

📖 https://docs.aws.amazon.com/bedrock/latest/userguide/titan-embedding-models.html

## S10. 十大考场陷阱 / Top 10 Exam Traps

**中文**（每条都来自题库干扰项规律）：
1. 升向量维度 ≠ 提升检索质量（只增成本）。
2. 静态阈值告警 ≠ "自动调整基线"（选 anomaly detection）。
3. CloudTrail ≠ 分布式追踪（追踪选 X-Ray）；CloudTrail 也 ≠ 护栏干预级日志（选 invocation logging / CloudWatch Logs）。
4. 只授权 agent ≠ 用户级授权（MCP 场景要 OAuth/Cognito）。
5. 缓存不是万能的——交互唯一时缓存是错误答案。
6. 固定延迟重试 = 重试风暴；必须退避 + jitter。
7. content filter 屏蔽不了竞争对手名字（用自定义词过滤）；denied topics 才是话题级拦截。
8. 部署后再合规检查 = 错（Guard 要放 CI/CD 预部署）。
9. 批处理/异步推理不能用于交互式实时场景。
10. "自建 SageMaker 端点/自建管道"选项在"least operational overhead"题里 99% 是干扰项。

**English**: ① Higher vector dims ≠ better retrieval. ② Static thresholds ≠ auto-adjusting baselines (pick anomaly detection). ③ CloudTrail ≠ distributed tracing (X-Ray) nor guardrail-intervention logs (invocation logging). ④ Authorizing the agent ≠ user-level authorization (MCP needs OAuth/Cognito). ⑤ Caching fails when interactions are unique. ⑥ Fixed-delay retries = retry storms; use backoff + jitter. ⑦ Content filters can't block competitor names (word filters); denied topics work at topic level. ⑧ Post-deploy compliance checks are wrong (Guard belongs in pre-deploy CI/CD). ⑨ Batch/async inference never fits interactive real-time. ⑩ Self-managed SageMaker/pipeline options are ~99% distractors in "least operational overhead" questions.

---

# 第六部分：备考策略与资源 / Part 6: Exam Strategy & Resources

## 学习路径 / Study Path

**中文**：
1. **第 1–2 天**：精读 Tier 1 六个知识点（KP1–KP6），对照速查表领域 1、2 刷题。
2. **第 3 天**：Tier 2（KP7–KP13）+ 领域 3、4 刷题——Guardrails 和 CloudWatch 套路必须形成条件反射。
3. **第 4 天**：Tier 3 + 领域 5 + 专家补充 S1–S10。
4. **第 5 天**：只刷速查表"场景信号 → 答案"列，做到 3 秒认题；复习十大陷阱。
5. **考试当天**：先扫题干找关键词（least operational overhead / real-time / data residency / least development effort），多选题先数要选几项再逐个排除。

**English**:
1. **Days 1–2**: Master Tier 1 (KP1–KP6); drill Domains 1–2 with the quick-reference table.
2. **Day 3**: Tier 2 (KP7–KP13) + Domains 3–4 — Guardrails and CloudWatch patterns must become reflexes.
3. **Day 4**: Tier 3 + Domain 5 + supplements S1–S10.
4. **Day 5**: Drill the "Signal → Answer" columns only until you recognize questions in 3 seconds; review the Top 10 traps.
5. **Exam day**: scan for keywords (least operational overhead / real-time / data residency / least development effort); for multi-select, note how many to pick before eliminating.

## 关键词词典 / Keyword Dictionary

| 英文关键词 | 指向答案 Answer |
|---|---|
| LEAST operational overhead / LEAST development effort | 托管服务（Knowledge Bases / KB 内置功能 / Step Functions / Glue） |
| real-time / bidirectional / streaming | WebSocket + 流式 API（InvokeModelWithResponseStream） |
| data residency / must remain in Europe | 地理范围 cross-Region inference profile |
| throttling at peak / no fixed cost | CRI 分散（on-demand）；steady throughput → Provisioned |
| hallucination / grounded in sources | RAG 接地 + contextual grounding check + citations |
| multi-turn conversation / context across turns | Converse API messages 数组 |
| human review / approval required | A2I / Step Functions waitForTaskToken |
| version + approval workflow + audit for prompts | Bedrock Prompt Management |
| without redeploying / change rules dynamically | AWS AppConfig |
| baseline deviation / auto-adjusting thresholds | CloudWatch anomaly detection |
| column-level / cross-account data lake access | Lake Formation LF-Tags |
| only approved models / enforce guardrail usage | IAM 条件键 + SCP |
| private connectivity | interface VPC endpoint (PrivateLink) |
| exact terms missed / acronyms | hybrid search（向量+BM25） |
| cross-section / long-document context | hierarchical chunking / semantic chunking |

## 官方资源 / Official Resources

- 考试指南 Exam Guide：https://docs.aws.amazon.com/aws-certification/latest/ai-professional-01/ai-professional-01.html
- AWS 认证主页：https://aws.amazon.com/certification/certified-generative-ai-developer-professional/
- Bedrock 用户指南：https://docs.aws.amazon.com/bedrock/latest/userguide/what-is-bedrock.html
- Well-Architected GenAI Lens：https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/welcome.html
- AWS Skill Builder（官方课程与模拟题）：https://skillbuilder.aws/
- Bedrock Workshop（动手实验）：https://github.com/aws-samples/amazon-bedrock-samples

---

*本指南基于 107 题题库（含官方解析）系统提炼，知识点按"题库频率 × 考试权重"排序。祝考试顺利！*
*Distilled from the 107-question bank with official explanations; ranked by frequency × weight. Good luck!*
