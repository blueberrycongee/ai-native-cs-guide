# Agent Loop

Agent Loop 是把 LLM 从一次性生成变成可执行系统的控制循环。循环本身不在模型里，而在应用代码或运行时里：系统把当前目标、状态和可用工具渲染给模型，模型给出下一步决策，代码校验并执行动作，再把观察结果写回状态，直到任务完成、需要人类确认或触达边界。

它解决的问题不是“让模型自动思考”，而是让任务推进依赖外部反馈。没有 loop，模型只能基于输入预测文本；有 loop，系统可以根据文件内容、测试结果、搜索结果、用户确认、工具错误和权限结果继续调整下一步。

## 和 workflow 的边界

不是所有“多步 LLM 应用”都需要 Agent Loop。

如果路径完全由代码预先写死，例如“先分类、再检索、最后总结”，更接近 workflow。Anthropic 在 [Building effective agents](https://www.anthropic.com/engineering/building-effective-agents) 里把 workflow 和 agentic system 区分开：workflow 通过预定义代码路径编排模型和工具，agent 则让模型动态决定流程和工具使用。这个区分很有用，因为 Agent Loop 会增加延迟、成本和调试难度，只有当任务确实需要模型根据反馈调整路径时才值得引入。

[[ReAct]] 是理解 Agent Loop 的经典入口。[ReAct paper](https://arxiv.org/abs/2210.03629) 提出并验证的是 reasoning trace 和 action 可以交替出现：推理帮助模型跟踪计划和处理异常，行动让模型从外部环境获取信息。生产系统不能直接把 ReAct prompt 当架构；真正要实现的是可控的状态更新、工具执行、权限、日志和停止条件。

## 状态和上下文

不要凭空给 Agent Loop 发明一张“标准状态表”。不同项目的状态结构不一样，更好的读法是看真实运行时把哪些东西从 prompt 里拿出来，交给代码维护。

以 [openai/codex](https://github.com/openai/codex) 为例，当前 `main` 分支 `3e7baa0` 这版代码里：

- [`Session`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/session.rs#L13-L32) 持有会话级对象，包括 `state`、`active_turn`、`input_queue`、`goal_runtime` 和各种服务。
- [`TurnContext`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/turn_context.rs#L50-L101) 是单个 turn 的上下文，里面有 `trace_id`、模型信息、工具模式、环境、工作目录、审批策略、权限 profile、截断策略、动态工具和 turn metadata。
- [`SessionState`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/state/session.rs#L22-L42) 放的是会话级可变状态，例如历史、rate limit、additional context、auto-compaction window、已选择的 connector 和已授予权限。
- [`record_conversation_items`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/mod.rs#L2502-L2515) 不只是把内容塞进内存历史，还会持久化 rollout，并通知正在观察 raw response item 的客户端。

这里能得到一个更可靠的结论：上下文不等于状态。上下文是下一次模型调用能看到的材料；状态是代码用来控制权限、恢复历史、压缩上下文、处理中断、记录执行轨迹和继续 turn 的事实记录。

## 工作机制

最小循环可以写成：

```text
initialize state
while not stopped:
  render context
  ask model for next decision
  validate decision
  execute action
  record observation
  update state
  check stop conditions
return final output or handoff reason
```

模型输出最好是结构化决策，而不是一段自由文本。常见决策类型包括：

- `tool_call`：调用某个工具。
- `final`：给出最终答案。
- `ask_user`：需要用户补充信息或批准高风险动作。
- `abort`：因为权限、证据不足或连续失败而停止。
- `handoff`：交给人类或另一个更合适的系统。

工具执行不应该由模型直接完成。模型只能提出“想调用什么工具、传什么参数”；代码负责 schema 校验、权限检查、超时、重试、结果截断和错误格式化。这个边界和 [[Tool Use]] 是同一个问题：Agent 从“说”变成“做”的地方，必须由代码守住。

## 工程形态

真实项目里，Agent Loop 通常不是一个孤立的 `while`，而是一组运行时组件：

- context builder：把目标、约束、可用工具、相关状态和最近观察渲染成模型输入。
- model adapter：封装不同模型 API、streaming、tool call 格式和重试策略。
- tool executor：执行工具，处理权限、超时、取消、错误和敏感信息。
- state store：保存任务状态、工具结果、摘要、checkpoint 和 session。
- scheduler：决定下一步是否继续、暂停、等待用户、重试或结束。
- tracer：记录每轮模型调用、工具调用、错误和停止原因。
- evaluator：用固定任务和 trace 判断 loop 改动有没有让系统变好。

[LangGraph persistence docs](https://docs.langchain.com/oss/python/langgraph/persistence) 能说明为什么状态持久化不是锦上添花：它把 graph state 按 step 保存成 checkpoint，用于 human-in-the-loop、memory、time travel debugging 和失败恢复。即使不使用 LangGraph，这些也是长任务 Agent Loop 必须面对的工程问题。

[OpenAI Agents SDK tracing docs](https://openai.github.io/openai-agents-python/tracing/) 则体现了 trace 的最小颗粒度：一次 agent run 里至少应该能看到 LLM generation、tool call、handoff、guardrail 等事件。[Trace grading](https://developers.openai.com/api/docs/guides/trace-grading) 进一步说明，评估 Agent 不能只看最终答案；给 trace 打结构化标签可以定位到底是工具选错、参数错、上下文错，还是停止条件错。

## Codex 里的 Agent Loop

Codex 的核心入口是 [`run_turn`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/turn.rs#L119-L133)。源码注释已经把 loop 讲得很直接：每次 sampling request 后，模型要么返回 function call，要么返回 assistant message。返回 function call 时，系统执行工具，并把工具输出放进下一次 sampling request；只返回 assistant message 时，系统把消息写进历史，然后认为这个 turn 完成。

```text
run_turn(input):
  record context updates, skill/plugin injections, hooks and user input
  loop:
    prompt = conversation history + model-visible tool specs
    output = run_sampling_request(prompt)
    if output.needs_follow_up or pending input:
      maybe compact context
      continue
    run stop hooks
    finish turn
```

这个伪代码不是通用模板，而是从 Codex 源码压缩出来的：

- `run_turn` 开始时会记录上下文更新、加载 skill/plugin 注入、执行 hooks、记录用户输入，并建立本 turn 的 `TurnDiffTracker`。对应源码在 [`turn.rs`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/turn.rs#L147-L218)。
- loop 内部会从会话历史构造下一次模型输入，然后调用 `run_sampling_request`。对应源码在 [`turn.rs`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/turn.rs#L219-L254)。
- sampling 完成后，Codex 用 `needs_follow_up` 决定是否继续。如果模型需要工具结果回灌、用户中途又追加了输入，或者上下文需要自动压缩，loop 会继续；如果不需要继续，就进入 stop hook 并结束 turn。对应源码在 [`turn.rs`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/turn.rs#L256-L366)。
- `run_sampling_request` 每次都会构造当前 turn 可见的工具路由器，创建 `ToolCallRuntime`，再用 `build_prompt` 把历史和工具 spec 交给模型。对应源码在 [`turn.rs`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/turn.rs#L897-L913) 和 [`turn.rs`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/turn.rs#L926-L981)。
- `built_tools` 会从 MCP、plugin、connector、dynamic tools 等来源构造本 turn 的 `ToolRouter`。对应源码在 [`turn.rs`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/turn.rs#L1028-L1126)。
- 模型流式输出完成一个 item 后，`handle_output_item_done` 会判断它是不是工具调用。是工具调用就记录 item、排队执行工具，并把 `needs_follow_up` 设成 true；不是工具调用就把 assistant message、reasoning 等转成 turn item 并完成记录。对应源码在 [`stream_events_utils.rs`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/stream_events_utils.rs#L405-L508)。
- `ToolCallRuntime` 负责真正调度工具，处理并行限制、取消、abort 输出和工具错误回传。对应源码在 [`parallel.rs`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/tools/parallel.rs#L62-L178) 和 [`parallel.rs`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/tools/parallel.rs#L186-L210)。

从 Codex 看 Agent Loop，比直接背 `observe -> act -> observe` 更有价值：真实系统里 loop 要围绕 turn、history、tool router、tool runtime、context compaction、pending input、stop hook 和 rollout persistence 组织。工具调用只是其中一环。

这也能解释为什么 coding agent 的难点不是“模型会写代码”这么简单。[SWE-agent paper](https://arxiv.org/abs/2405.15793) 讨论的是 agent-computer interface 对软件工程任务表现的影响；Codex 源码给了更具体的工程形态：模型看到哪些工具、工具调用如何进入 runtime、结果如何回到 history、什么时候继续 turn、什么时候停止。

Codex 的 Agent Loop 可以沿着 `run_turn`、`run_sampling_request`、`handle_output_item_done`、`ToolRouter` 和 `ToolCallRuntime` 这条路径理解。UI 或 README 展示的是用户视角，运行时结构还包括 turn、history、tool router、tool runtime、context compaction、pending input、stop hook 和 rollout persistence。

## 停止条件

Agent Loop 必须能停，但它不是靠一句 prompt 让模型“觉得差不多了就停”。以 Codex 为例，真正决定继续还是结束的是运行时，而不是模型自己的承诺。

可以把 Codex 的一次 turn 理解成这样：模型先给出一个结果，运行时检查这个结果后再决定下一步。如果模型只是给出最终回复，并且没有其他事情要处理，这一轮就可以结束。如果模型要求调用工具，Codex 会先执行工具，把结果写回对话历史，再让模型看着新的结果继续。工具调用本身不是结束点，它只是下一轮输入的来源。

还有一些情况会让 Codex 暂时不能结束。比如用户在模型运行过程中又追加了输入，系统会把这类 pending input 纳入后续处理。再比如上下文快满了，但任务还没结束，Codex 会先尝试压缩上下文，而不是直接停掉。还有 stop hook 这种机制：表面上模型已经给完答案了，但运行时检查后发现还需要补充约束或做收尾，也可以把新的提示写回历史，让模型继续。

有些停止不是“任务完成”，而是“不能继续”。用户中断时，Codex 会停止当前 turn，并尽量把正在跑的工具取消或收尾。遇到权限边界时，比如高风险命令或额外权限请求，Codex 不会让模型自己决定能不能绕过去，而是发起审批；审批失败、超时或被取消，就按没有权限处理。遇到上下文窗口溢出、用量限制或不可重试错误时，系统会发出错误事件并结束当前 turn，让用户之后再决定怎么继续。

这说明一个重点：Codex 的停止机制不是简单的“最多跑 N 步”。在我看的这条主路径里，没有看到一个核心的 `max_steps` 作为主要停止条件。它更像一个事件驱动的 turn loop：有工具结果要回灌就继续，有新用户输入就继续，需要压缩上下文就先压缩，需要审批就等待审批，遇到中断或错误就停。你自己写 Agent Loop 时可以加最大轮数，但那是额外保险，不是 Codex 这段主流程的核心设计。

对应源码可以从这几处看：[`run_turn`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/turn.rs#L256-L366) 负责决定继续还是结束；[`handle_output_item_done`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/stream_events_utils.rs#L414-L445) 把工具调用变成后续输入；[`ToolCallRuntime`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/tools/parallel.rs#L135-L178) 处理工具取消；审批和权限请求在 [`session/mod.rs`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/mod.rs#L1954-L2210)。

## 常见失败模式

### 把 loop 写成无限自治

没有预算和停止条件时，Agent 会消耗 token、重复搜索、反复修改同一处文件，甚至在任务已经完成后继续行动。生产系统要把“继续”视为需要证明的选择，而不是默认动作。

### 把工具错误当普通文本

如果工具失败只返回“出错了”，模型很难恢复。错误应该结构化，例如 `permission_denied`、`timeout`、`not_found`、`schema_invalid`、`rate_limited`。不同错误对应不同策略：换参数、请求权限、等待重试、缩小任务或停止。

### 状态只存在上下文里

上下文会被截断、摘要和重排。真正重要的状态应该落在代码数据结构或持久化存储里。否则长任务一旦中断，系统无法知道已经做过什么，也无法解释为什么做过。

### trace 粒度太粗

只记录最终回答没有调试价值。至少要记录每轮的模型决策、工具参数、工具结果、错误类别、重试次数和停止原因。涉及敏感数据时，trace 要脱敏或关闭原始输入输出保存。

### 迷信多 Agent

很多任务的问题不是 Agent 数量不够，而是单个 loop 的状态、工具、权限和 eval 没做好。多 Agent 会放大调试难度；只有当任务天然需要不同权限边界、不同上下文视角或明确交接协议时，才值得引入 [[Single Agent vs Multi-Agent]]。

## 最小运行形态

一个很小但可验证的本地 Agent Loop 通常包含：

- 工具有 schema 和权限检查。
- 每轮有结构化 trace。
- 工具失败会回到状态，而不是被吞掉。
- 有最大轮数、时间和费用预算。
- 最终答案能引用工具观察到的证据。
- 中断后能知道上一步做到哪里。
- 至少有一组固定 eval 任务，用来比较 prompt、模型或工具改动。

只会“模型调用工具再回答”，但没有状态、停止条件、trace 和 eval 的系统，更接近 tool-calling demo。

## 相关概念

- [[Agent]]
- [[Tool Use]]
- [[ReAct]]
- [[Context Engineering]]
- [[Evals]]
- [[Security]]
- [[Cost]]
- [[Streaming Response]]

## 参考资料

- [ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629)：理解 reasoning、action 和 observation 交替出现的基本思想。
- [Anthropic: Building effective agents](https://www.anthropic.com/engineering/building-effective-agents)：理解 workflow 和 agent 的边界，以及为什么要优先使用简单、可组合的模式。
- [LangGraph Persistence](https://docs.langchain.com/oss/python/langgraph/persistence)：看 checkpoint、thread、state history、replay 和失败恢复如何进入 agent runtime。
- [OpenAI Agents SDK Tracing](https://openai.github.io/openai-agents-python/tracing/)：看 agent run 中 LLM generation、tool call、handoff、guardrail 等事件如何被记录。
- [OpenAI Trace grading](https://developers.openai.com/api/docs/guides/trace-grading)：理解为什么评估 Agent 要看 trace，而不只看最终输出。
- [openai/codex `run_turn`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/session/turn.rs#L119-L133)：看真实 coding agent 如何组织 turn loop。
- [openai/codex `handle_output_item_done`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/stream_events_utils.rs#L405-L508)：看模型输出如何被分流成工具执行或普通 assistant message。
- [openai/codex `ToolCallRuntime`](https://github.com/openai/codex/blob/3e7baa00e43419967d90d6ad9cef40f58d5ac89f/codex-rs/core/src/tools/parallel.rs#L62-L178)：看工具调度、并行控制、取消和错误回传。
- [SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering](https://arxiv.org/abs/2405.15793)：用 coding agent 例子理解工具界面和执行反馈如何影响 Agent Loop 表现。
