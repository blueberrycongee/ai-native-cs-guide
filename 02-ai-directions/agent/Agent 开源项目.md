# Agent 开源项目

这页列 Agent 方向的开源项目入口。

这里不做排序，也不判断项目优劣。每个项目只记录它代表的系统形态，以及后续可以拆解的设计主题。

## Coding Agent

### Codex

- GitHub：[openai/codex](https://github.com/openai/codex)
- 类型：coding agent
- 代表形态：在本地代码仓库中读文件、改代码、运行命令和测试，并把结果回传给开发者的终端 Agent。
- 可拆解设计：工作区上下文、命令执行、权限边界、patch / diff、测试反馈循环、开发者协作界面

### opencode

- GitHub：[anomalyco/opencode](https://github.com/anomalyco/opencode)
- 官网：[opencode.ai](https://opencode.ai/)
- 类型：terminal coding agent
- 代表形态：面向终端开发体验的 coding assistant，把模型、TUI、文件变更和开发工具集成在一起。
- 可拆解设计：终端 UI、模型供应商适配、LSP 集成、文件变更追踪、多模型配置、交互式开发工作流

## Personal Agent

### OpenClaw

- GitHub：[openclaw/openclaw](https://github.com/openclaw/openclaw)
- Docs：[docs.openclaw.ai](https://docs.openclaw.ai/)
- 类型：personal agent platform
- 代表形态：运行在个人环境里的常驻 Agent 平台，接入多个渠道、workspace 和本地能力。
- 可拆解设计：gateway、channels、skills、workspace、messaging、local-first gateway、安全边界

### Hermes Agent

- GitHub：[NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
- Docs：[hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs)
- 类型：self-hosted agent / skill-based agent
- 代表形态：自托管的 skill-based Agent，把 skills、memory、messaging gateway 和 MCP 放在同一个系统里。
- 可拆解设计：skills 组织、memory、messaging gateway、MCP、自托管部署、长期上下文

## Agent Framework / Orchestration

### LangGraph

- GitHub：[langchain-ai/langgraph](https://github.com/langchain-ai/langgraph)
- 类型：stateful agent workflow framework
- 代表形态：用 graph / workflow 表达有状态 Agent 流程。
- 可拆解设计：状态机、节点和边、checkpoint、人类介入、workflow 和 agent loop 的边界

### AutoGen

- GitHub：[microsoft/autogen](https://github.com/microsoft/autogen)
- 类型：multi-agent framework
- 代表形态：用多个 Agent 的 conversation 和协作关系组织任务。
- 可拆解设计：multi-agent conversation、角色分工、消息路由、工具调用、协作任务管理

### CrewAI

- GitHub：[crewAIInc/crewAI](https://github.com/crewAIInc/crewAI)
- 类型：role-based multi-agent framework
- 代表形态：用角色、任务和流程描述 multi-agent 协作。
- 可拆解设计：角色建模、任务编排、crew / flow、工具绑定、任务执行过程
