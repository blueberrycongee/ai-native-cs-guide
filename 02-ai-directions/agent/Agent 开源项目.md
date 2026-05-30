# Agent 开源项目

Agent 方向的开源项目大致分成 coding agent、personal agent、memory / state 和 orchestration framework。

## Coding Agent

### Codex

- GitHub：[openai/codex](https://github.com/openai/codex)

Codex 是 coding agent 形态的代表项目之一。它把本地代码仓库、命令执行、文件修改、测试反馈和开发者协作放在同一个终端工作流里，能看到工作区上下文、权限边界、patch / diff 和测试循环如何被组织起来。

### opencode

- GitHub：[anomalyco/opencode](https://github.com/anomalyco/opencode)
- 官网：[opencode.ai](https://opencode.ai/)

opencode 也是 terminal coding agent，但更明显地呈现了终端 UI 和开发者体验这一层。它把模型供应商适配、LSP、文件变更追踪、多模型配置和交互式开发工作流放在一起。

## Personal Agent

### OpenClaw

- GitHub：[openclaw/openclaw](https://github.com/openclaw/openclaw)
- Docs：[docs.openclaw.ai](https://docs.openclaw.ai/)

OpenClaw 更接近 personal agent platform。它关注常驻 Agent 如何接入多个渠道、workspace 和本地能力，里面会遇到 gateway、channels、skills、messaging、local-first gateway 和安全边界这些问题。

### Hermes Agent

- GitHub：[NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
- Docs：[hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs)

Hermes Agent 是 self-hosted / skill-based agent。它把 skills、memory、messaging gateway、MCP、自托管部署和长期上下文放在同一个系统里，和单纯的聊天界面不是同一种问题。

## Memory / State

### Mem0

- GitHub：[mem0ai/mem0](https://github.com/mem0ai/mem0)
- Docs：[docs.mem0.ai](https://docs.mem0.ai/)

Mem0 是面向 AI Agents 的 memory layer。它把长期信息抽取、用户 / 会话作用域、metadata、向量检索、冲突处理和 add / search / update / delete 操作做成独立系统，可以作为 Agent runtime 之外的 memory provider 使用。

## Agent Framework / Orchestration

### LangGraph

- GitHub：[langchain-ai/langgraph](https://github.com/langchain-ai/langgraph)

LangGraph 用 graph / workflow 表达有状态 Agent 流程。它把状态机、节点和边、checkpoint、人类介入、workflow 和 agent loop 的边界放到框架抽象里。

### Dify

- GitHub：[langgenius/dify](https://github.com/langgenius/dify)
- 官网：[dify.ai](https://dify.ai/)

Dify 更接近 agentic workflow 和 LLM 应用开发平台，而不是只给代码调用的轻量库。它把 workflow、RAG pipeline、agent capabilities、模型管理、工具接入、观测和应用发布放在一个产品化系统里，适合观察 Agent 如何从框架抽象进入可部署应用。

### Eino

- GitHub：[cloudwego/eino](https://github.com/cloudwego/eino)
- Docs：[cloudwego.io/docs/eino](https://www.cloudwego.io/docs/eino/)

Eino 是 CloudWeGo 生态里的 Go 语言 LLM / Agent 应用开发框架。它围绕 ChatModel、Tool、Retriever、ChatTemplate 等组件抽象组织应用，也提供 Agent Development Kit、graph / workflow composition、streaming、callback 和 human-in-the-loop 相关能力。

### AutoGen

- GitHub：[microsoft/autogen](https://github.com/microsoft/autogen)

AutoGen 用多个 Agent 的 conversation 和协作关系组织任务，重点在 multi-agent conversation、角色分工、消息路由、工具调用和协作任务管理。

### CrewAI

- GitHub：[crewAIInc/crewAI](https://github.com/crewAIInc/crewAI)

CrewAI 用角色、任务和流程描述 multi-agent 协作。它的抽象集中在角色建模、任务编排、crew / flow、工具绑定和任务执行过程。
