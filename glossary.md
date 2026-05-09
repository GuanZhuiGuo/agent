# 术语表 Glossary

> 面向 AI PM 的中英对照术语表。
> 不求学术严谨，但求"PM 能在会上正确使用"。

---

## A

- **Agent / 智能体**：能感知、思考、使用工具并记忆的软件系统。输出行动，而不止输出文字。
- **Agentic RAG / 代理式 RAG**：让 Agent 根据需要**多次、自主**检索的 RAG 模式。
- **Agent Loop / Agent 主循环**：观察 → 思考 → 行动 → 判断是否结束 的循环。

## B

- **Background Agent / 后台 Agent**：脱离即时对话，跑分钟～小时级的长任务的 Agent。
- **Blackboard / 黑板模式**：多 Agent 通过共享工作区协作的模式。
- **BM25**：关键词检索的经典算法，RAG 中常与向量混合。

## C

- **Chain-of-Thought (CoT) / 思维链**：让 LLM 先写推理再给答案的提示技巧。
- **Chunking / 切分**：把长文档切成 chunk（片段）的过程。RAG 的关键环节。
- **Context Window / 上下文窗口**：模型一次能看到的最大 token 数。
- **Copilot / 副驾模式**：人是司机，AI 辅助。

## D

- **Debate / Peer Review Pattern**：多个 Agent 给出不同方案，一个 Judge 评选的模式。
- **DAG (Directed Acyclic Graph)**：有向无环图，工作流的常见形态。

## E

- **Embedding / 向量嵌入**：把一段文字映射成向量，用于语义相似度计算。
- **Episodic Memory / 情景记忆**：特定事件的记忆（用户上次做了 X）。
- **Eval / 评估**：对 AI 产品质量的量化测量。

## F

- **Faithfulness / Groundedness**：答案有多少部分有参考资料支撑。
- **Function Calling / 函数调用**：LLM 输出结构化指令请求调用某函数。= Tool Use。

## G

- **Golden Set / 黄金集**：人工标注的标准评测数据集。
- **Guardrails / 栏杆**：步数、成本、话题红线等约束。

## H

- **Hallucination / 幻觉**：LLM 一本正经地编造错误内容。
- **Handoff / 移交**：Multi-Agent 中从一个 Agent 移交到另一个。
- **HITL (Human-in-the-loop) / 人类在环**：关键节点需要人工确认/审阅的机制。
- **Hybrid Retrieval / 混合检索**：向量 + 关键词（BM25）联合检索。

## J

- **Judge / LLM-as-Judge**：让一个 LLM 按 rubric 给另一个 LLM 的输出打分。

## K

- **Knowledge Base / 知识库**：供 Agent 查询的外部知识集合，常配 RAG。
- **KV (Key-Value) Memory**：按键值查询的结构化记忆。

## L

- **LLM (Large Language Model) / 大语言模型**：Agent 的"大脑"。
- **LLM-as-Judge**：见 Judge。
- **Long-term Memory / 长期记忆**：跨会话持久化的记忆（L3/L4/L5）。
- **Lost in the Middle**：模型对上下文中间部分的注意力降低的现象。

## M

- **Memory / 记忆**：让 Agent 跨时间保持信息连续的机制。
- **MemGPT**：把 Agent 记忆当作分页式"内存管理"的开源项目。
- **Multi-Agent / 多智能体**：由多个分工的 Agent 协作完成任务。
- **MRR (Mean Reciprocal Rank)**：检索指标，平均倒数排名。

## O

- **Orchestrator–Worker / 编排-工人**：主 Agent 分派、子 Agent 执行的拓扑。
- **Observability / 可观测性**：能查到每一步输入、输出、耗时、成本。

## P

- **Plan-and-Execute / 先规划后执行**：一种 Agent 规划模式。
- **PII / 个人可识别信息**：姓名、手机号、身份证等敏感字段。
- **Prompt / 提示词**：给 LLM 的输入文本。
- **Prompt Injection / 提示注入**：攻击者通过输入/文档塞入恶意指令。
- **Procedural Memory / 过程性记忆**：Agent 的"做法/技能"记忆。

## R

- **RAG (Retrieval-Augmented Generation) / 检索增强生成**：先检索再生成，让 LLM 用上外部知识。
- **ReAct (Reason + Act)**：最主流的 Agent 循环模式。
- **Reflection / 反思**：Agent 对自己结果的自我评估和修正。
- **Rerank / 重排**：对召回结果用更精准的模型二次排序。
- **Rubric / 评分标准**：主观评估的评分轴定义。

## S

- **Semantic Memory / 语义记忆**：通用知识型记忆（L5）。
- **Session Memory / 会话记忆**：本次对话中的状态（L2）。
- **Skills / 技能**：可调用的特定能力包（与"工具"接近，但更高层）。
- **SubAgent / 子 Agent**：主 Agent 启动的、有独立上下文的协作 Agent。
- **Swarm / 蜂群**：OpenAI 推出的多 Agent 动态路由模式。
- **System Prompt / 系统提示词**：定义 Agent 身份、规则、能力的提示词。

## T

- **Token**：LLM 的计量单位；大致 1 汉字 ≈ 1–1.5 token。
- **Tool / 工具**：Agent 能调用的函数 / API / 动作。
- **Trace / 追踪**：一次任务的完整执行链路记录。
- **TSR (Task Success Rate) / 任务完成率**：Agent 产品的常用北极星指标。
- **TTFT (Time To First Token)**：首个 token 的响应时间。

## U

- **UX for Agents**：Agent 专属的体验设计原则（透明、可控、可追溯、可纠正…）。

## V

- **Vector DB / 向量数据库**：存向量并支持相似度检索的数据库。

## W

- **Working Memory / 工作记忆**：当前 LLM 调用内的上下文（L1）。
- **Workflow / 工作流**：人为预定义的步骤图；可在节点内嵌入 AI。

---

> 本术语表会随课程迭代持续更新。若你希望扩充某个词条，欢迎提 PR。


---

## 模块 09 补充：框架与生态词汇

- **LangChain**：LLM 组件与链（Chain）式编排库。适合线性管道、RAG。
- **LCEL（LangChain Expression Language）**：LangChain 的声明式组合语法，最擅长线性/简单分支管道。
- **LangGraph**：基于有向图的 Agent 编排框架，支持循环、分支、持久状态、人审中断。LangChain 团队推荐的 Agent 默认方案。
- **AutoGen**：微软开源的对话式 Multi-Agent 框架，研究血统，强在 Agent 间会话。
- **CrewAI**：以"角色 + 任务 + 工艺"抽象的 Multi-Agent 框架，上手快；生产化常被质疑。
- **LlamaIndex / Workflows**：以 RAG / 数据索引起家，2025 年后推出 Workflows 进入 Agent 编排。
- **OpenAI Agents SDK**：OpenAI 的简化 Agent 开发 SDK；核心原语：Agent / Handoff / Guardrails。
- **Claude Agent SDK**：Anthropic 围绕 Claude 模型的 Agent 框架，擅长工具调用与复杂推理。
- **Pydantic AI / Vercel AI SDK / Mastra**：更轻量、偏产品工程师使用的 Agent / AI 库。
- **MCP（Model Context Protocol）**：Anthropic 开放的 Agent↔工具 / 数据源通用协议。2026 已成事实标准。
- **A2A（Agent-to-Agent Protocol）**：Google 推出的 Agent 间通信协议，补齐 Multi-Agent 通信层。
- **Handoff**：Agent 之间显式移交任务的动作，见于 OpenAI Agents SDK / Swarm。
- **Interrupt / HITL 中断**：LangGraph 等框架原生支持的"暂停等待人审"机制。
- **pgvector**：PostgreSQL 的向量扩展，适合已有 PG + 千万级以内向量。
- **Pinecone / Qdrant / Weaviate / Milvus / Chroma**：常见向量数据库；各自在托管度、规模、混合检索、DX 等维度不同。
- **LangSmith / Langfuse / Arize / Braintrust / Helicone / Phoenix**：主流 Agent 可观测 & 评估平台。
- **Session-level Trace**：以"整个任务会话"为单位的追踪，是 Agent 观测与单次 LLM 监控的核心差别。
- **Build vs. Buy vs. Framework**：自研、买平台、用开源框架三者之间的选型三角。
