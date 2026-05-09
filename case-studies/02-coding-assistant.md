# 案例 2：编程助手 Agent（类 Cursor / Claude Code）

> 这是最"火"的 Agent 场景之一，也是**记忆分层、长任务、可观测性**集中展示的地方。

---

## 背景

目标：做一个 IDE 内置的编程 Agent，能：
- 回答代码相关问题（问答式）
- 编辑多文件（修改式）
- 执行多步任务（如"给所有 API 加日志"）（Agent 式）
- 长任务后台跑（如"修 CI 里这个 flaky 测试"）（Background Agent）

---

## 1. 能力光谱定位

这个产品是**从左到右都覆盖**的典型，按模式划分：

| 模式 | 能力光谱 | 典型用法 |
|---|---|---|
| Completion / 补全 | Chat | 你打字它补 |
| Chat | Chat + RAG | 你问它答 |
| Edit | 工具调用 Agent | 一次性改代码 |
| Agent | 规划 Agent | 多步自动完成 |
| Background Agent | 自主 Agent | 后台长任务 |

🧭 PM 视角：**产品演进路径要和用户信任阶梯（模块 08 · 8.7）对齐**。

---

## 2. 规划模式

- Completion：纯生成（无规划）
- Chat：CoT
- Edit：Plan-and-Execute（先列改动计划，再批量 apply）
- Agent：**ReAct + Reflection**
- Background Agent：Plan-and-Execute + 多层 Reflection + 任务化 UI

---

## 3. 记忆设计：分层最吃香的场景

| 层 | 存什么 |
|---|---|
| L1 工作 | 当前文件、当前 selection、当前 cursor |
| L2 会话 | 本次对话历史、已尝试的 patch |
| L3 用户 | 代码风格（引号 / 缩进 / 注释风格）、偏好的库、是否喜欢详细解释 |
| L4 项目 | 这个仓库的架构总结、模块职责、关键依赖、README 摘要 |
| L5 系统 | 通用编程技能："修 TypeScript 类型错误的通用步骤"等 |

### 关键设计：L4 项目记忆

这是编程 Agent 的杀手锏：

- 初始化时自动扫描仓库 → 生成架构摘要 → 保存
- 每次对话前把 L4 摘要注入上下文
- 用户改了架构文档可以手动刷新

典型实现：`.agent/memory.md`（类似 Cursor 的 rules 或 Claude Code 的 CLAUDE.md）

**🧭 PM 决策点**：L4 要不要让用户可编辑？要。这让 Agent 变得更懂"你们这个项目的约定"。

---

## 4. 知识库 / RAG

几种可能的 RAG 源：
- 官方库文档（语义召回，避免版本错乱）
- 项目 README / 架构文档
- 代码本身（按函数切分 embedding）
- 历史 commit / PR 摘要

**特殊点**：代码 RAG 要**和源码树结构结合**：
- 不只返回片段，还返回"这个片段在哪个文件哪个函数里"
- 元数据：`file_path`, `symbol_name`, `language`, `commit_hash`

---

## 5. 工具清单

| 工具 | 类别 | HITL |
|---|---|---|
| read_file | Read | A |
| search_code | Retrieval | A |
| list_files | Read | A |
| edit_file | Write-可撤销 | B（默认预览，Apply 前确认） |
| run_shell | Computation | B / C（取决于命令） |
| run_tests | Computation | A |
| web_search | Retrieval | A |
| git_commit | Write | C |
| git_push | Write | **C** 强制 |

⚠️ `run_shell` 要有沙箱 + 命令白名单 + 禁用破坏性命令（`rm -rf /`）。

---

## 6. Multi-Agent？

**需要，且形态多样：**

### Sub-Agent 模式（类 Claude Code）

- 主 Agent 发现任务很大 → 启动 Sub-Agent
- Sub-Agent 有独立上下文（解决上下文爆炸问题）
- Sub-Agent 只返回**摘要**

典型用法：
- "给这 20 个 API 都加日志" → 每 5 个 API 一个 Sub-Agent 并行
- "分析这个仓库" → 多个研究 Sub-Agent

### 反射 Agent（Reviewer）

- 每次 patch 前，由独立的 Reviewer Agent 检查
- 看逻辑、看风格、看安全隐患

🧭 PM 要决定：哪些操作前启用 Reviewer？成本 ÷ 2 还是质量 × 1.5？

---

## 7. 栏杆

- 单任务最大步数：30（后台任务可更多，但要显示进度）
- 单任务最大成本：$0.50（普通），$5（后台长任务）
- 单任务最大时间：10 min（普通），1 h（后台）
- `edit_file` 变更范围限制（防止跨仓库失控）
- 禁止修改 `.env` / secrets 文件
- 禁止自动 `git push` 到保护分支

---

## 8. UX 关键点

- 展示思考过程（展开/折叠）
- 展示每一步工具调用（可点开看详情）
- 改代码前展示 **Diff**，用户 Apply
- 长任务进度条（"正在修改 3/20 个文件"）
- **可中断**按钮永远在
- Artifact 列表：这次任务产出的所有文件

### "信任阶梯"体现
- Step 1：补全 / 问答（纯读）
- Step 2：Edit 模式（改，但要 Apply）
- Step 3：Agent 模式（改 + 自动 Apply，但可 undo）
- Step 4：Background Agent（长任务自主）

---

## 9. 评估方案

- **代码 benchmark**：SWE-bench / HumanEval 等公开 + 自建
- 真实任务黄金集：100 条（包含 Bug 修复、重构、加功能）
- Rubric：是否通过测试、是否符合风格、是否破坏其他文件
- 红队：诱导泄露代码、诱导执行危险命令、Prompt Injection（通过注释/文档）

📌 编程 Agent 有一个非常特殊的优势：**有测试就是客观评判**。PM 可以把"跑通测试"作为硬指标。

---

## 10. 指标

| 指标 | 目标 |
|---|---|
| 首次一次通过率（First-try Success） | ≥ 60% |
| 最终通过率（允许反思/重试） | ≥ 80% |
| 平均步数 | ≤ 10（普通）/ ≤ 30（后台） |
| 平均成本 / 任务 | ≤ $0.5 |
| 单次耗时 | ≤ 2min（普通） |
| 用户 Apply 率 | ≥ 70% |
| Undo 率 | ≤ 10% |

---

## 11. 特殊挑战

### 11.1 上下文爆炸
大仓库 = 上下文窗口远远装不下。
对策：
- 按需读文件（工具调用驱动）
- 生成项目摘要（L4 记忆）
- Sub-Agent 拆分

### 11.2 成本波动
一次 Agent 任务可能 5 步也可能 50 步。
对策：
- 成本上限 + 提前预警
- 模型分级：规划用大模型，执行用中等
- 缓存频繁的代码搜索结果

### 11.3 安全
执行代码、改文件、连网、推仓库 → 全是高风险。
对策：
- 沙箱 + 白名单
- Diff 必须展示
- 危险命令硬拒
- 远程执行必须有可撤销机制

---

## 学到的事

1. **编程 Agent 是记忆分层的最佳教学样本**：L1–L5 全部用上。
2. **信任阶梯要产品化**：用户从 completion 一路用到后台 Agent，是一条产品路线图。
3. **Multi-Agent 的"正当场景"之一**：上下文隔离 + 并行，二者皆有。
4. **客观评估（测试通过）是金矿**：有测试就不怕评估失焦。
5. **UX 决定信任**：Diff、进度、可撤销、可中止是四件套。
