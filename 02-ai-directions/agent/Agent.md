# Agent

Agent 是这个仓库优先展开的方向。它适合用项目带动学习，因为你很快会碰到模型、上下文、工具、权限、日志和评测。

这里先用一个朴素定义：

> Agent 是一个能围绕目标，在上下文、工具和反馈之间循环工作的系统。

它不是“套一层 ChatGPT API”，也不是自动规划一切的营销词。判断一个系统是不是值得当 Agent 学，要看它是否真的能读取状态、调用工具、吸收反馈，并在边界内继续推进任务。

## 学习顺序

1. 先读 [[Agent 概念]]，建立最小概念地图。
2. 再读 [[Agent Loop]] 和 [[Tool Use]]，理解最小循环和工具边界。
3. 补 [[Context Engineering]]、[[Evals]]、[[Security]]，避免只做会说话的 demo。
4. 读 [[Agent 开源项目]]，挑一个 coding agent 或 orchestration 项目看源码入口。
5. 做本地资料整理 Agent，完成后再考虑 [[Single Agent vs Multi-Agent]] 和 [[Skills]]。

不要一开始就陷进框架 API。框架会变，循环、工具、状态、权限和评测不会消失。

## 必做项目

第一个项目是本地资料整理 Agent：

- 输入：一个本地 Markdown 仓库和用户目标。
- 工具：`list_files`、`read_file`、`search_text`。
- 输出：摘要、缺口、修改建议，必须引用读过的文件。
- 约束：只能读取指定目录；每一步工具调用写日志；最大循环轮数固定。

这个项目小，但足够暴露 Agent 的核心问题：上下文选择、工具权限、循环停止、失败恢复和输出校验。

## 参考项目

- [LangGraph](https://docs.langchain.com/oss/python/langgraph)：代表有状态、可恢复、可观测的 agent/workflow runtime。先看 quickstart、durable execution、streaming 和 human-in-the-loop。
- [OpenAI Agents SDK](https://platform.openai.com/docs/guides/agents-sdk/)：代表 SDK 形态的 agent、tool、handoff 和 trace 抽象。先看 agents、tools、tracing。
- [Microsoft AutoGen](https://autogenhub.github.io/autogen/docs/Use-Cases/agent_chat/)：代表 multi-agent conversation。先看 agent chat、examples 和工具执行方式。
- [CrewAI](https://docs.crewai.com/en/index)：代表角色化 crew/flow 编排。先看 concepts、quickstart 和 flows。
- [opencode](https://github.com/anomalyco/opencode)：代表终端 coding agent。重点看模型适配、终端交互和文件变更处理。

不要只看 star。优先看 docs、examples、issue、release、日志能力和权限设计。

## 工程实践清单

- 工具 schema 清楚，参数由代码校验。
- 工具权限不靠 prompt，读写范围由代码限制。
- 每轮记录目标、模型输出、工具调用、工具结果和停止原因。
- 对慢请求、工具失败和用户取消有处理。
- 上下文有 token 预算，长工具结果会摘要。
- 输出能引用证据，不能引用时拒绝或说明缺口。
- 至少有 10 个固定 eval 任务，改 prompt 或模型后能重跑。

## 必学知识

必须能写代码或读源码：

- [[Agent Loop]]
- [[Tool Use]]
- [[Context Engineering]]
- [[Streaming Response]]
- [[API Auth]]
- [[Evals]]

必须理解机制：

- [[LLM]]
- [[Prompting]]
- [[Context Window]]
- [[Security]]
- [[Cost]]

知道存在即可，等项目需要再深入：

- [[Memory]]
- [[Skills]]
- [[ReAct]]
- [[Single Agent vs Multi-Agent]]

## 阶段验收标准

做到下面这些，才算完成 Agent 入门阶段：

- 能跑通本地资料整理 Agent，并用日志证明它读了哪些文件。
- 能解释每个工具为什么存在、权限边界在哪里。
- 能处理工具失败、无证据、用户取消和循环上限。
- 能用 eval 说明一次 prompt 或模型改动有没有变好。
- 能指出自己的系统和真实 coding agent 的差距：并发、长期状态、沙箱、权限、观测、成本和回滚。

## 继续深入

下一步不要急着堆 multi-agent。先从自己的项目里挑一个痛点深入：

- 上下文混乱：读 [[Context Engineering]] 和 [[Memory]]。
- 工具越权：读 [[Security]] 和 [[API Auth]]。
- 调试困难：补日志、trace 和 [[Evals]]。
- 长任务不稳定：看 LangGraph 的 durable execution。
- 多角色确实有价值：再读 [[Single Agent vs Multi-Agent]]。
