# 模块 09 · Agent 框架与生态（PM 选型指南）

> 目标：让 PM 能看懂研发的技术选型方案、能提出有信息量的问题、能在 LangChain / LangGraph / AutoGen / CrewAI / LlamaIndex / OpenAI Agents SDK / Claude Agent SDK 之间做出"合格的判断"。
>
> **本模块 NOT 教你写代码**。讲的是：**为什么选 A 不选 B、选错的代价是什么、PM 要跟研发问清楚什么**。

---

## 9.1 PM 为什么要懂框架

做 Chatbot 时代的 PM 可以不懂框架，Agent 时代不行。原因：

1. **选型错 → 产品天花板被锁住**
   - 选了线性链（LangChain LCEL）→ 后期要做循环/分支/人审要重写
   - 选了过于重型的 Multi-Agent 框架 → 调试成本和账单双重爆炸
2. **选型错 → 工程解释不清"为什么做不到"**
   - 你看 Demo 觉得 2 周就能上线，结果研发说要 2 个月，多半是在补框架的"抽象税"
3. **选型错 → 被供应商绑定**
   - 有些框架深度绑定某个 LLM 厂商（如 OpenAI Agents SDK 偏向 OpenAI 家族）
   - 有些绑定自家观测平台（LangSmith / Braintrust）

**PM 的角色**：不替研发做选型，但要能**提出选型应该回答的问题**（9.9 对话手册）。

---

## 9.2 Agent 技术栈全景图

用这张图把生态里的东西"分层归位"。每一层都是 PM 会在 PRD 或评审中遇到的决策点。

```
┌─────────────────────────────────────────────────────────────────────┐
│  L7  终端产品层        ChatGPT / Claude / Cursor / Manus / Coze     │
├─────────────────────────────────────────────────────────────────────┤
│  L6  应用层             你的 Agent 产品（PM 管的地方）              │
├─────────────────────────────────────────────────────────────────────┤
│  L5  编排框架层         LangGraph / LangChain / AutoGen / CrewAI /  │
│       (Orchestration)  LlamaIndex Workflows / OpenAI Agents SDK /   │
│                         Claude Agent SDK / Pydantic AI              │
├─────────────────────────────────────────────────────────────────────┤
│  L4  协议与互联层        MCP（工具协议） / A2A（Agent 间协议）       │
├─────────────────────────────────────────────────────────────────────┤
│  L3  记忆与检索层        Vector DB（Pinecone / Qdrant / pgvector…）  │
│                          Embedding 服务 / Rerank 服务                │
├─────────────────────────────────────────────────────────────────────┤
│  L2  模型层              OpenAI / Anthropic / Google / 国内厂商 /   │
│                          开源模型（Llama / Qwen / DeepSeek…）       │
├─────────────────────────────────────────────────────────────────────┤
│  L1  基础设施层          GPU / 推理服务 / 网关 / 缓存 / 队列        │
├─────────────────────────────────────────────────────────────────────┤
│  〰〰  横切  可观测性 & 评估  (LangSmith / Langfuse / Arize…)        │
│       安全（Guardrails / PII / 审计）                               │
└─────────────────────────────────────────────────────────────────────┘
```

🧭 PM 视角：**你和研发讨论的 90% 是 L4–L6**。L1–L2 是基础，L7 是竞品参照。

---

## 9.3 LangChain vs LangGraph（最常被问的一组）

这是 2024–2026 最多 PM 搞混的两个词，**必须分清**：

### 9.3.1 一句话版

> **LangChain 是"组件库 + 链"；LangGraph 是"状态图"。**

- **LangChain**：把 LLM 调用、工具、记忆、RAG 打包成可组合组件。核心抽象：**Chain**（线性流）。
- **LangGraph**：基于图（Graph）的执行引擎，允许**循环、分支、条件跳转、人审中断、持久状态**。由 LangChain 团队推出，现在是 LangChain 团队推荐的 Agent 默认方案。

### 9.3.2 什么时候选哪个

```
            你要做的是…
                │
        ┌───────┴────────┐
        ▼                ▼
   线性管道             有循环 / 分支 / 长任务 / 人审中断
        │                │
        ▼                ▼
   LangChain（LCEL）   LangGraph
   典型：              典型：
   - RAG 问答           - 工具循环 Agent
   - 文档摘要           - 多步规划 Agent
   - 单轮 chat+召回     - 人审关卡中断恢复
                        - 长任务持久化
```

行业共识（2026 年前后）：**任何"真正的 Agent"都在往 LangGraph 这类状态图方向走**，LangChain 更多作为组件库和 RAG 工具链继续使用。

> 引用参考：业界多份 2026 技术综述反复强调"线性链不足以支撑自主 Agent"，[一份 2026 综述](https://www.digitalapplied.com/blog/langchain-vs-langgraph-comparison-2026) 把这形容为 *"Cyclic Graphs 之年"*。内容已改写。

### 9.3.3 PM 视角的取舍对照表

| 维度 | LangChain (LCEL) | LangGraph |
|---|---|---|
| 核心抽象 | 线性/简单分支 | 状态图（节点 + 边） |
| 最适合 | RAG、文档问答、检索链 | 有循环 / 分支 / 人审 / 持久化的 Agent |
| 状态管理 | 组件级，较简单 | 显式共享状态，复杂但强 |
| 调试复杂度 | 低 | 中-高（要看 trace） |
| 学习曲线 | 低 | 中 |
| 能表达的产品形态上限 | Copilot / Chatbot / RAG | 真 Agent、长任务、Multi-Agent |
| 典型失败模式 | 后期想加循环 / 人审，全部重写 | 过度设计状态导致开发慢 |

### 9.3.4 一个 PM 必须警惕的场景

**反模式**：产品上线 V1 用 LangChain LCEL 做线性 RAG，效果不错；V2 要求"Agent 能调工具循环自查"，研发预估要 1 个月重写。

**✅ 建议**：
- V1 若明确会演进到 Agent → **直接用 LangGraph**，哪怕 V1 只用它的"串行子集"。
- V1 只是 RAG / Chatbot 且未来 6 个月不会变 Agent → **LangChain 完全够用**。

🧭 **PM 决策建议**：问研发一个问题——"**如果 3 个月后要加'人类在环审批'和'工具循环自查'，这个架构要不要重写？**" 答案是"要"，就考虑换。

---

## 9.4 其他主流 Agent 框架（2026 快速扫描）

> 市场上活跃的 Agent 框架已 20+，以下是 PM 最可能听到的 6 个。**内容是综合公开技术综述并改写**。

### 9.4.1 AutoGen（微软）

- **定位**：研究血统，擅长 Agent 之间对话（Conversational Multi-Agent）
- **强项**：代码生成 / 研究型任务；支持复杂会话式协作
- **弱项**：状态管理、生产级可观测相对单薄
- **PM 何时考虑**：偏研究 / 探索 / 内部工具；不太适合严苛生产级体验

### 9.4.2 CrewAI

- **定位**：把 Multi-Agent 包装成"角色 + 任务 + 工艺（process）"的高层抽象
- **强项**：上手极快、Demo 惊艳、角色叙事友好（"研究员 / 作家 / 审稿人"）
- **弱项**：生产化之后常被吐槽"状态控制、回滚、审批、成本可控性不足"；很多团队上生产后迁移到 LangGraph
- **PM 何时考虑**：快速验证多角色想法（POC）；**慎用于严肃生产**

> 多份 2026 综述提到："CrewAI 的 Demo 很好，生产不行；很多团队最终迁移到更严格的状态控制框架。" 内容改写自 [ZenML 综述](https://www.zenml.io/blog/crewai-alternatives)。

### 9.4.3 LlamaIndex（含 Workflows）

- **起点**：RAG 之王（LlamaIndex 原本是做"数据+索引"的）
- **新进**：推出 Workflows 之后也能做多步 Agent
- **强项**：复杂异构数据源的 RAG（结构化 + 文档 + 表格）
- **PM 何时考虑**：RAG 场景为主 + 逐步 Agent 化；2026 年的 LangChain vs LlamaIndex 已经**明显相互重叠**（都能做 RAG，也都能做 Agent）

### 9.4.4 OpenAI Agents SDK

- **定位**：OpenAI 官方"极简 Agent 开发"SDK；基于 **Agent + Handoff + Guardrails** 三原语
- **强项**：代码量最少、最快上手；与 OpenAI 模型 + 工具调用高度一致
- **弱项**：生态绑 OpenAI；复杂拓扑 / 持久化弱于 LangGraph
- **PM 何时考虑**：**你和 OpenAI 深度绑定** + 想快速做个像样的 Agent

### 9.4.5 Claude Agent SDK（Anthropic）

- **定位**：Anthropic 配合 Claude 的 Agent 框架；天然契合 Claude 的 **Tool Use + Extended Thinking + Computer Use**
- **强项**：工具调用稳定、文件系统/代码工具成熟；Anthropic 长文档处理和推理优势
- **弱项**：生态偏 Claude
- **PM 何时考虑**：你的产品**以 Claude 为主力模型** + 需要稳健工具调用（编程 / 复杂推理）

### 9.4.6 Pydantic AI / Vercel AI SDK / Mastra（轻量派）

- **共同点**：不是"大而全"的框架，而是**类型安全 + 易集成 + 面向产品团队**
- Vercel AI SDK 偏前端（React / Next.js 生态）；Pydantic AI 偏 Python 后端类型化；Mastra 偏 TS 全栈
- **PM 何时考虑**：团队更像"产品工程师"而非"AI 研究员"，要在现有应用中嵌入 Agent 能力

### 9.4.7 PM 的选型决策树

```
                    你的主要场景？
                         │
   ┌─────────────────────┼─────────────────────┐
   ▼                     ▼                     ▼
 RAG 为主            单 Agent + 工具          Multi-Agent
   │                     │                     │
   ▼                     ▼                     ▼
LangChain /         OpenAI Agents SDK       LangGraph
LlamaIndex          / Claude Agent SDK      （默认）
                    / LangGraph
                                             需快速 POC？
                                                │
                                         ┌──────┴──────┐
                                         是            否
                                         │            │
                                         ▼            ▼
                                      CrewAI        LangGraph /
                                      (仅 POC)     AutoGen

  特别地：
  - 团队是产品工程师（非 AI 专家）+ 嵌入现有应用？→ Vercel AI SDK / Pydantic AI
  - 你深度依赖某个模型家族？→ OpenAI Agents SDK / Claude Agent SDK
```

---

## 9.5 向量数据库（L3 层）

PM 不需要记性能数字，需要记**选型逻辑**：

### 9.5.1 主流选项（2026）

| 方案 | 一句话 | PM 何时选 |
|---|---|---|
| **pgvector** | Postgres 扩展，把向量索引塞进现有关系数据库 | 团队已用 Postgres + 向量量 < 千万级 + 不想加新组件 |
| **Pinecone** | 托管服务，最省心；按用量付费 | 不想自运维 + 快速上线 |
| **Qdrant** | Rust 写的，自建延迟最低 | 要自建 + 追求极低延迟 |
| **Weaviate** | 混合检索（语义 + 关键词）+ GraphQL | 语义 + 关键词融合是硬需求 |
| **Milvus** | 大规模（亿级以上） | 超大规模 / AI 原生企业场景 |
| **Chroma** | DX 最友好，常被拿来做 POC | 早期开发 / 小规模 |

> 成本参考：多份 2026 综述指出，在 10M 向量规模，pgvector 和 Qdrant Cloud 价格明显低于 Pinecone；100M+ 规模则自建方案成本优势显著。摘自 [LeanOpsTech 对比](https://leanopstech.com/blog/vector-database-cost-comparison-2026/)，内容已改写。

### 9.5.2 PM 该问的 3 个问题

1. 我们预计多少向量？（千万 / 亿 / 十亿）→ 决定是否要离开 pgvector
2. 自运维还是托管？→ 决定是否 Pinecone
3. 查询需要"语义 + 关键词"混合吗？→ 决定是否 Weaviate
4. 要不要支持**租户级权限过滤**？→ 不是所有库都一样友好

### 9.5.3 ⚠️ 常见产品陷阱

- **一开始选错**：POC 用 Chroma 很快，生产时要迁移才发现工作量大
- **只看价格**：忽视运维、隔离、备份、冷启动
- **重建成本**：换 Embedding 模型意味着**整库重建**，PM 要在 PRD 里说明"多久换一次"

---

## 9.6 可观测性平台：Agent 的"黑匣子" 去哪看（L6 横切层）

模块 07 讲了为什么需要 **Trace**，这里补具体工具：

### 9.6.1 主流工具对照

| 工具 | 定位 | 特点 | PM 何时选 |
|---|---|---|---|
| **LangSmith**（LangChain 家） | Agent 原生观测 + Eval | 和 LangChain/LangGraph 深度集成 | 栈就是 LangChain 系 |
| **Langfuse** | 开源 / 可自建 | 成本可控、被 ClickHouse 收购（2026）后仍开源 | 注重数据主权 / 自建 |
| **Arize / Phoenix** | 从传统 ML 监控进化而来 | 机器学习团队原班底 | 已有 ML 团队 / 企业级 |
| **Braintrust** | 强 Eval 定位 | 评估能力强，和 CI 集成友好 | 评估驱动开发 |
| **Helicone** | 代理式观测 | 不用改代码，走代理 | 快速加观测 |
| **Datadog LLM Obs** | 传统 APM 扩展 | 和公司现有基础设施打通 | 企业已是 DD 客户 |

> 2026 年的一个重要洞察：**传统 APM 看不了 Agent 错误**。Agent 的错误通常是"多步因果链"，需要 **session-level trace**，而不是单次 API 监控。内容改写自 [Latitude 综述](https://latitude.so/blog/best-ai-agent-observability-tools-2026-comparison)。

### 9.6.2 PM 视角：不选工具，选"能力"

不管研发选哪个工具，PM 要确保能看到：

- [ ] 一次任务的**完整 trace**（每步 LLM 调用 + 工具调用）
- [ ] 每步的**输入输出 + 耗时 + 成本**
- [ ] 按**用户 / 租户 / 任务类型**切分的聚合
- [ ] 线上"**差评的那次任务**"能立即找到 trace
- [ ] 支持**评估集回跑**（发版回归测试）

### 9.6.3 PM 对话手册

问研发：
1. "我们的 trace **保留多久**？能按差评反查吗？"
2. "**PII** 会进 trace 吗？怎么脱敏？"（**这是合规红线！**）
3. "评估集能**在 CI 里跑**吗？还是手动？"

⚠️ 隐患：很多团队开观测只开了 **LLM 调用**层，没开 **工具调用**层；线上事故发生时，根本看不到"是哪个工具错了"。

---

## 9.7 MCP / A2A：2026 PM 必须懂的两个协议

2024 底 Anthropic 推出的 **MCP**（Model Context Protocol），到 2026 已成**事实标准**。Google 推出的 **A2A**（Agent-to-Agent）补上了 Multi-Agent 通信层。

> 参考：2026 年初 Linux Foundation 接管 MCP 治理；公开统计显示 MCP SDK 月下载量在 2026 Q1 已经接近亿级，超过 10000 个公共 MCP 服务器。摘自 [Digital Applied](https://www.digitalapplied.com/blog/mcp-97-million-downloads-model-context-protocol-mainstream) 与 [truthifi](https://truthifi.com/education/state-of-mcp-2026-ai-agents-custom-connectors)。内容已改写。

### 9.7.1 一句话定义

- **MCP**：Agent 和**工具 / 数据源**之间的"USB-C"（统一接口协议）
- **A2A**：Agent 和**其他 Agent** 之间的"USB-C"

### 9.7.2 为什么 PM 要关心

过去：每接一个工具 / SaaS，研发写一份自定义集成代码。新模型或框架换一个，又重写一遍。
现在：**一次 MCP server，任何支持 MCP 的 Agent 都能用**。

对 PM 的直接意义：

1. **集成周期大幅缩短**：供应商给你一个 MCP server，不是一个 OpenAPI 文档
2. **生态兼容性**：你的产品支持 MCP，就天然能被 Claude Desktop / ChatGPT / Cursor 等调用（**分发渠道**）
3. **产品形态会变**：用户会期望"我能把这个 Agent 接入我的其他工具"

### 9.7.3 MCP 相关 PM 决策

| 场景 | 决策 |
|---|---|
| 我们做企业 SaaS | 考虑**提供 MCP server**，让客户的 Agent 能直接集成 |
| 我们做 Agent 产品 | 考虑**消费 MCP server**，获得海量即插即用工具 |
| 我们做内部工具 | 用 MCP 减少每次接新系统的工程量 |

### 9.7.4 ⚠️ MCP 不是银弹

- **权限和审计**：连任何 MCP server 就像给 Agent 发 OAuth，要有**权限矩阵**
- **提示注入放大**：MCP 返回的内容可能是攻击向量（对齐模块 07 的 Prompt Injection 防护）
- **成本**：每个 MCP 连接都意味着工具列表更长 → 上下文更大 → 成本更高（见 4.4 "工具过多的选困难"）

🧭 PM 决策点：**不是接得越多越好**。要按场景把工具分组加载（dynamic tools）。

---

## 9.8 "Build vs. Buy vs. Use Framework" 三角

PM 一个高频问题：自研 or 框架 or 第三方平台？

```
          自研（From Scratch）
                 ▲
                 │ 最灵活、最贵、最长周期
                 │
                 │
     框架（LangGraph 等）
                 │
                 │ 站在巨人肩膀，但要懂取舍
                 │
                 ▼
       平台（Coze / Dify / Flowise / Langflow）
                   
     最快交付，但天花板 = 平台能力
```

决策建议：

| 你的阶段 | 建议 |
|---|---|
| 早期验证（< 3 个月） | 平台 or 轻框架（Vercel AI SDK） |
| 严肃产品（3–12 个月） | 框架（LangGraph / Claude Agent SDK） |
| 核心竞争力（> 1 年） | 框架 + 部分自研（状态管理 / 评估 / Trace） |
| 研究 / 前沿探索 | 自研 + 论文式实现 |

⚠️ 最贵的错误：**一上来就自研**。一个团队花 6 个月造轮子，比用 LangGraph 慢 3 倍 + 不稳定。

---

## 9.9 🛠 PM 对话手册：评审技术方案时的 12 个问题

拿到研发的 Agent 技术方案，顺序问这 12 个：

### 架构取舍
1. **我们为什么选 X（框架）而不是 Y？**（考察他们有没有做对比）
2. **6 个月后如果我们要加"循环 + 人审"，这个架构要不要重写？**
3. **我们对某个模型 / 平台有多大程度的锁定？要换掉要多久？**

### 状态与记忆
4. **一次任务的完整状态**在哪里？崩溃后能恢复吗？
5. **用户跨设备继续**同一个任务行不行？

### 观测与评估
6. 我能看到一次任务的**完整 trace** 吗？
7. 线上差评能反查到**那一次的输入输出**吗？
8. **评估集回跑**有没有集成到发版流程？

### 安全与合规
9. **PII** 在哪里、怎么脱敏？Trace 里有没有？
10. Agent 如果调用了**不该调的工具**，系统层能不能拦？

### 成本与扩展
11. 一次任务的成本**上限**怎么设？
12. 我们接入 **MCP** 了吗？哪些工具走 MCP、哪些自己实现？

**📌 如果研发 12 个问题中有 5 个答不上来，方案还不成熟，不要立项。**

---

## 9.10 给 PM 的学习建议（别去学 API）

PM 不需要跑通 LangGraph 的 hello-world，但建议：

1. **读 3 份官方文档的"概念章节"**（不是 API）：
   - LangGraph Concepts（重点：State / Node / Edge / Interrupt）
   - Anthropic 的 Building Effective Agents（对你和研发都是圣经）
   - MCP 的 "Core Concepts"（3 个原语：Resources / Tools / Prompts）
2. **玩一次现成 Agent 产品**：Claude Desktop + MCP、Cursor、Coze、Dify——体验"它"到底在做什么
3. **跟研发做一次 "trace review"**：请他打开一次真实 trace，逐步讲解每一步

这样你比 80% 的 PM 更懂底层实际发生了什么，却不用写一行代码。

---

## 9.11 本模块 Checklist

设计 Agent 产品时，请确保你和研发达成一致：

- [ ] 编排框架选型（LangGraph / AutoGen / CrewAI / Agents SDK / 自研）+ 理由
- [ ] 状态与持久化方案（能否断点续跑）
- [ ] 向量库选型（pgvector / Pinecone / Qdrant / …）+ 迁移代价估算
- [ ] 可观测性工具（LangSmith / Langfuse / Arize / …）+ PII 脱敏
- [ ] 评估集 + 发版回归机制
- [ ] MCP 集成策略（提供 / 消费 / 不接）
- [ ] Build vs Buy 决策 + 3 年锁定风险
- [ ] 成本上限与模型分级路由

---

## 9.12 延伸阅读（均为公开综述，内容需自行甄别）

- Anthropic · *Building effective agents*（无论什么框架都要读）
- LangChain 官方 · *When to use LangChain vs LangGraph*
- *Model Context Protocol* 官网与 Anthropic 博客
- OpenAI · *Agents SDK* 文档与 *A practical guide to building agents*
- Google · *A2A Protocol* 规范
- 多家 2026 综述（文中已链接）：
  - [LangChain vs LangGraph 2026 比较](https://www.digitalapplied.com/blog/langchain-vs-langgraph-comparison-2026)
  - [OpenAI Agents SDK vs LangGraph vs CrewAI 对照](https://www.digitalapplied.com/blog/openai-agents-sdk-vs-langgraph-vs-crewai-matrix-2026)
  - [Agent 可观测性综述](https://latitude.so/blog/ai-agent-observability-tools-comparison-2026)
  - [向量数据库 2026 对比](https://www.digitalapplied.com/blog/vector-databases-for-ai-agents-pinecone-qdrant-2026)
  - [MCP 采用现状](https://www.digitalapplied.com/blog/mcp-adoption-statistics-2026-model-context-protocol)

> 文中引用自第三方综述的内容均已改写以符合授权要求。

---

> 返回 → [README](../README.md) | [大纲](../SYLLABUS.md) | [模块 08](08-productization.md)
