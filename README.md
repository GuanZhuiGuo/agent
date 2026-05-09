# AI 产品经理的 Agent 学习课程

> 一套为 **AI 产品经理（AI PM）** 量身设计的 Agent 系统化课程。
> 不讲代码细节，讲**产品决策、系统设计权衡、用户体验与评估落地**。

---

## 为什么是这门课

市面上 Agent 的内容，要么是给工程师的框架教程（LangChain、LangGraph、AutoGen 的 API），要么是给大众的科普（"Agent 就是会用工具的 AI"）。

**AI PM 需要的是第三种**：

- 看得懂系统图，但重点是**能在里面做取舍**；
- 能和研发讨论记忆策略、RAG 分块、Agent 拓扑，而不是被动接收方案；
- 能定义**成功指标**，而不只是"感觉对话更聪明了"；
- 能识别哪些需求**不该用 Agent**（这往往比会用更重要）。

本课程的目标，就是把这三件事装进你的脑子。

---

## 目标受众

- **AI 产品经理**（0–3 年 AI 产品经验，有基础 LLM 使用经验）
- 想从 "Chatbot PM" 升级到 "Agent PM" 的产品人
- 想系统补齐 Agent 能力认知的 **技术负责人 / 设计师 / 解决方案架构师**

不需要你会写代码，但你需要：
- 理解 LLM 基本概念（Prompt、Token、上下文窗口、幻觉）
- 用过至少一个 Agent 产品（Cursor、Claude Code、Manus、Coze、扣子、Dify 等都可以）

---

## 课程地图

```
┌─────────────────────────────────────────────────────────────┐
│  模块 00  课程导论 & Agent 全景地图                         │
├─────────────────────────────────────────────────────────────┤
│  模块 01  Agent 基础与 PM 视角                              │
│           ↓                                                  │
│  ┌──────────────────────────────────────────────────┐       │
│  │ 模块 02  记忆设计   ⭐ 重点                      │       │
│  │ 模块 03  知识库/RAG  ⭐ 重点                     │       │
│  │ 模块 04  工具与 Action                           │       │
│  │ 模块 05  规划、推理与工作流                      │       │
│  │ 模块 06  Multi-Agent 系统设计  ⭐ 重点            │       │
│  └──────────────────────────────────────────────────┘       │
│           ↓                                                  │
│  模块 07  评估、可观测与安全                                │
│  模块 08  产品化、成本与落地                                │
├─────────────────────────────────────────────────────────────┤
│  附录：模板集 / 案例学习 / 练习题 / 术语表                  │
└─────────────────────────────────────────────────────────────┘
```

---

## 仓库结构

```
.
├── README.md                     # 本文件
├── SYLLABUS.md                   # 教学大纲 / 课时安排
├── modules/                      # 8 个核心教学模块
│   ├── 00-introduction.md
│   ├── 01-agent-foundation.md
│   ├── 02-memory-design.md       ⭐
│   ├── 03-knowledge-base-rag.md  ⭐
│   ├── 04-tools-and-actions.md
│   ├── 05-planning-and-workflow.md
│   ├── 06-multi-agent.md         ⭐
│   ├── 07-evaluation-safety.md
│   └── 08-productization.md
├── templates/                    # PM 可直接复用的画布 / 模板
│   ├── agent-prd-template.md
│   ├── memory-canvas.md
│   ├── multi-agent-canvas.md
│   └── eval-scorecard.md
├── case-studies/                 # 3 个完整案例
│   ├── 01-customer-support-agent.md
│   ├── 02-coding-assistant.md
│   └── 03-deep-research-agent.md
├── exercises/                    # 练习题 + 期末项目
│   └── exercises.md
└── glossary.md                   # 中英对照术语表
```

---

## 推荐学习路径

### 路径 A：系统学习（推荐，约 3 周）

按模块 00 → 08 顺序阅读，每个模块：
1. 读讲义 → 2. 做练习 → 3. 填一次模板 → 4. 回到自家产品对照

### 路径 B：带着问题学（1 周速成）

根据你当前面临的问题，直接跳到对应模块：

| 你的当前困境 | 去哪 |
|---|---|
| "用户抱怨 Agent 记不住事" | 模块 02 记忆设计 |
| "知识库答得不准，幻觉多" | 模块 03 RAG |
| "工具调用经常报错、死循环" | 模块 04 + 05 |
| "要不要拆成多个 Agent？" | 模块 06 Multi-Agent |
| "怎么证明 Agent 真的变好了" | 模块 07 评估 |
| "成本太高，怎么优化" | 模块 08 产品化 |

### 路径 C：团队共学（2 周）

- 每周 2 次，每次讨论 1 个模块
- 配合 `case-studies/` 做 Case Review
- 配合 `templates/` 为团队正在做的 Agent 产品填一份

---

## 课程使用约定

- **⭐ 标注** = 重点模块，时间不够优先读这些
- **🧭 决策点** = 需要 PM 拍板的关键选择
- **🛠 模板** = 可直接复用的画布 / 清单
- **📊 指标** = 上线后要盯的数据
- **⚠️ 陷阱** = 常见翻车点

---

## 配套模板一览

| 模板 | 何时使用 |
|---|---|
| [Agent PRD 模板](templates/agent-prd-template.md) | 立项时 |
| [记忆设计画布](templates/memory-canvas.md) | 设计记忆方案时 |
| [Multi-Agent 画布](templates/multi-agent-canvas.md) | 考虑拆成多 Agent 时 |
| [评估记分卡](templates/eval-scorecard.md) | 验收 / 版本对比时 |

---

## 致谢 & 参考

本课程综合了 Anthropic、OpenAI、Google、LangChain、Andrew Ng 等公开分享的 Agent 设计实践，以及大量真实产品 case 沉淀。参考文献在各模块末尾给出。

Happy building. Agent 不是未来，是现在。
