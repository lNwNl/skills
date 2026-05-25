```

我正在整理 AI Agent 工具链文档。请使用一两句话描述此项目对agent的作用。输出格式（严格遵守，无需输出其他无关内容）：
### [标题](链接)
描述

```

## 自建 Skills

### agent-concepts-\*

Agent protocol 概念系列 — MCP、A2A、AGENTS.md、Agent Skills、三层协议栈架构。从工具层到协调层到应用层，覆盖 AI Agent 标准生态。

### cybernetics-thinking

控制论系统思维 — 诊断系统不稳定、分析反馈循环与涌现行为、逆向工程黑盒。适用于复杂系统故障排查与自适应架构设计。

### deepseek-brand-guidelines

DeepSeek 官方品牌视觉规范 — 色彩系统与视觉标识。确保 DeepSeek 相关产物品牌一致性。

### opencode-investigation

OpenCode Agent 执行记录调查 — 追踪 tool call 详情、排查 Agent 行为异常、分析输出缺失与超时原因。

### research-beyond-knowledge

引导 Agent 突破训练数据截止边界，发现新兴概念与技术。适用于技术全景调研与未知领域探索。

---

### [GitButler MCP Server](https://docs.gitbutler.com/features/ai-integration/mcp-server)

此项目作为模型上下文协议（MCP）服务器，赋予 AI Agent 直接读写 Git 仓库、管理分支及执行版本控制操作的标准化接口，使其能够自主完成代码提交与代码审查任务。

### [GitNexus](https://github.com/abhigyanpatwari/GitNexus)

[OpenCode + GitNexus: Give OpenCode Real Codebase Memory, Every Developer Use this](https://www.youtube.com/watch?v=bhsd9MXfccg&t=8s)

GitNexus 将代码库索引为结构化的知识图谱，通过 MCP 协议为 AI Agent 提供深度架构感知，使其能够分析调用链和变更影响，避免在复杂代码编辑中出现逻辑遗漏。

### [andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills)

该项目将 Andrej Karpathy 对 LLM 编程缺陷的洞察固化为一套核心原则，旨在通过系统性的 Prompt 指令约束 Agent 的决策行为，减少其在复杂工程任务中出现的盲目假设、过度设计及无意义的代码改动。

### [Matt Pocock's Skills](https://github.com/mattpocock/skills)

该项目提供了一套标准化的能力描述模式，通过将复杂任务拆解为可组合的离散技能，帮助 AI Agent 实现更精准的任务规划与工具调用。

### [热门 Skills](https://context7.com/skills)

这是一个面向 AI 助手的可复用能力分发平台，允许开发者通过命令行快速为 Cursor 或 Claude 等 Agent 集成最新的外部文档与第三方 API 技能。

### [Context7](https://github.com/upstash/context7)

该项目提供了一个自动化的文档索引与 RAG 工具，能够将不同来源的外部文档转化为 Agent 可直接调用的结构化上下文，从而扩展 LLM 的知识边界。

### [Exa Search API](https://exa.ai/docs/reference/search-api-guide)

为 AI Agent 提供专为大模型设计的神经搜索能力，通过语义理解检索互联网实时信息并直接返回清洗后的网页内容，使其能够更精准、高效地获取外部知识。

### [Playwright MCP Server](https://playwright.dev/docs/getting-started-mcp)

通过模型上下文协议（MCP）赋予 AI Agent 自动化浏览器操作能力，使其能够直接进行网页截图、点击、输入及抓取动态内容，实现复杂的 Web 端任务自动化。

### [MCP Memory Server](https://github.com/modelcontextprotocol/servers/tree/main/src/memory)

通过图结构（Graph）为 AI Agent 提供持久化记忆存储方案，允许 Agent 跨会话创建、读取和搜索实体及其相互关系，从而构建复杂的知识网络和长期上下文。

### [superpowers](https://github.com/obra/superpowers)
这是一个为 Claude Code、Cursor、Codex 等编程 Agent 设计的可组合技能框架，通过提供头脑风暴、测试驱动开发（TDD）、子代理协作与代码审查等结构化工作流技能，将 AI 助手从单次任务执行者升级为具备完整软件工程方法论的自主开发伙伴。

### [Skills 列表](https://skills.sh/)

Skills.sh 是一个开放的 Agent 技能生态系统，提供可复用的能力模块（Skills），让 AI Agent 能通过单一命令快速集成程序性知识与专业工作流，显著扩展其任务执行边界与领域专业性。

### [terminal-mcp](https://github.com/mkpvishnu/terminal-mcp)
该项目为 AI Agent 提供基于 MCP 协议的交互式终端会话能力，使 Agent 能够创建持久化 PTY 会话并执行 SSH、REPL、数据库 CLI 及 TUI 应用等需要状态保持和特殊键输入的复杂终端任务。