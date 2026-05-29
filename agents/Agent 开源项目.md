# Agent 开源项目

这页只做一件事：列出 Agent 方向可以看的项目，并说明从哪里入手。

不用把每个项目源码都读完。先知道它们分别代表哪种 Agent 形态，再挑一两个和自己项目相关的继续看。

## 必看项目

### Codex

- GitHub：[openai/codex](https://github.com/openai/codex)
- 类型：coding agent
- 可以学：终端内 coding agent、本地仓库操作、代码修改、测试运行、权限边界、开发者体验

Codex 可以作为 coding agent 的代表项目来看。重点不在“模型会写代码”，而在它怎么在真实代码库里读文件、改文件、运行命令、处理测试反馈，并和开发者协作。

可以重点看：

- 它如何组织本地工作区
- 它如何处理命令执行和权限
- 它如何把代码修改、测试结果和用户反馈连成循环
- 它如何通过文档和配置影响 Agent 行为

### OpenCode

- GitHub：[anomalyco/opencode](https://github.com/anomalyco/opencode)
- 官网：[opencode.ai](https://opencode.ai/)
- 类型：terminal coding agent
- 可以学：TUI、模型适配、LSP 集成、文件变更追踪、终端开发者体验

opencode 可以和 Codex 对比着看。它更偏终端 AI coding assistant，可以用来观察 coding agent 的交互层怎么设计。

可以重点看：

- 终端 UI 如何降低 Agent 使用门槛
- 如何适配不同模型供应商
- 如何追踪和展示文件变更
- 如何把 LSP 等传统开发工具接入 AI 工作流

### OpenClaw

- GitHub：[openclaw/openclaw](https://github.com/openclaw/openclaw)
- Docs：[docs.openclaw.ai](https://docs.openclaw.ai/)
- 类型：personal agent platform
- 可以学：常驻 Agent、多通道接入、skills、workspace、messaging、local-first gateway、安全边界

OpenClaw 不是 coding agent。它更像运行在个人环境里的常驻 Agent 平台，可以观察 Agent 怎么从“聊天窗口”变成能接入多个渠道并执行任务的系统。

可以重点看：

- gateway 如何连接模型、通道、工具和 workspace
- skills 系统如何扩展 Agent 能力
- 多通道消息如何进入同一个 Agent 系统
- 安全模型如何处理陌生输入、权限和沙箱

### Hermes Agent

- GitHub：[NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
- Docs：[hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs)
- 类型：self-hosted agent / skill-based agent
- 可以学：skills、memory、messaging gateway、MCP、自托管、长期上下文和持续学习

Hermes Agent 可以用来看长期能力和用户上下文怎么管理。它比工具调用示例更完整，把 skills、memory、messaging gateway 和自托管部署放到同一个系统里。

可以重点看：

- skills 如何被组织、安装和调用
- memory 如何影响跨会话体验
- messaging gateway 如何让 Agent 不只存在于 CLI
- MCP 和外部工具如何进入 Agent 能力体系

## 扩展项目

这些项目不必一开始就深挖，但应该知道它们大概解决什么问题。

- [LangGraph](https://github.com/langchain-ai/langgraph)：看有状态 graph / workflow 风格的 Agent 编排。
- [AutoGen](https://github.com/microsoft/autogen)：看 multi-agent conversation 和多 Agent 协作框架。
- [CrewAI](https://github.com/crewAIInc/crewAI)：看角色化 multi-agent 和任务编排。

## 看开源项目时看什么

不要只看 README 和 star 数。

重点看：

- 项目解决的是 coding、personal assistant、workflow，还是 multi-agent orchestration
- Agent 的输入从哪里来：CLI、Web、API、消息平台、文件系统
- Agent 能调用哪些工具
- 工具调用有没有权限控制和确认机制
- 是否有状态、记忆、workspace 或 session
- 失败时如何恢复
- 是否有 eval、测试、日志或 tracing

如果一个项目只展示“模型自动完成任务”，但不解释失败、权限、成本和安全边界，那它更像 demo，不要作为主要学习对象。
