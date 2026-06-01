# Agent Loop

Agent Loop 是把 LLM 从一次性生成变成可执行系统的控制循环。循环本身不在模型里，而在应用代码或运行时里：系统把当前目标、状态和可用工具渲染给模型，模型给出下一步决策，代码校验并执行动作，再把观察结果写回状态，直到任务完成、需要人类确认或触达边界。

它解决的问题不是“让模型自动思考”，而是让任务推进依赖外部反馈。没有 loop，模型只能基于输入预测文本；有 loop，系统可以根据文件内容、测试结果、搜索结果、用户确认、工具错误和权限结果继续调整下一步。

## 和 workflow 的边界

不是所有“多步 LLM 应用”都需要 Agent Loop。如果路径完全由代码预先写死，例如“先分类、再检索、最后总结”，更接近 workflow。Anthropic 在 [Building effective agents](https://www.anthropic.com/engineering/building-effective-agents) 里把 workflow 和 agentic system 区分开：workflow 通过预定义代码路径编排模型和工具，agent 让模型动态决定流程和工具使用。这个区分很有用，因为 Agent Loop 会增加延迟、成本和调试难度，只有当任务确实需要模型根据反馈调整路径时才值得引入。

[[ReAct]] 是理解 Agent Loop 的经典入口。[ReAct paper](https://arxiv.org/abs/2210.03629) 提出并验证的是 reasoning trace 和 action 可以交替出现。生产系统不能直接把 ReAct prompt 当架构；真正要实现的是可控的状态更新、工具执行、权限、日志和停止条件。

## 状态和上下文

不要凭空给 Agent Loop 发明一张“标准状态表”。不同项目的状态结构不一样，更可靠的读法是看真实运行时把哪些东西从 prompt 里拿出来，交给代码维护。

以 [openai/codex](https://github.com/openai/codex) 当前主分支的代码为例：

- `Session` 持有会话级对象：`state`、`active_turn`、`input_queue`、`goal_runtime` 和各种服务。
- `TurnContext` 是单个 turn 的上下文：`trace_id`、模型信息、工具模式、环境、工作目录、审批策略、权限 profile、截断策略、动态工具和 turn metadata。
- `SessionState` 放会话级可变状态：历史、rate limit、additional context、auto-compaction window、已选择的 connector 和已授予权限。
- `record_conversation_items` 不只是把内容塞进内存历史，还会持久化 rollout，并通知正在观察 raw response item 的客户端。

一个更可靠的结论：上下文不等于状态。上下文是下一次模型调用能看到的材料；状态是代码用来控制权限、恢复历史、压缩上下文、处理中断、记录执行轨迹和继续 turn 的事实记录。

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

[LangGraph persistence docs](https://docs.langchain.com/oss/python/langgraph/persistence) 说明为什么状态持久化不是锦上添花：graph state 按 step 保存成 checkpoint，用于 human-in-the-loop、memory、time travel debugging 和失败恢复。[OpenAI Agents SDK tracing docs](https://openai.github.io/openai-agents-python/tracing/) 体现 trace 的最小颗粒度：一次 agent run 里至少能看到 LLM generation、tool call、handoff、guardrail 等事件。[Trace grading](https://developers.openai.com/api/docs/guides/trace-grading) 进一步说明，评估 Agent 不能只看最终答案；给 trace 打结构化标签可以定位到底是工具选错、参数错、上下文错，还是停止条件错。

## Codex 里的 Agent Loop

Codex 的核心入口是 `run_turn`，源码注释已经把 loop 讲得很直接：每次 sampling request 后，模型要么返回 function call，要么返回 assistant message。返回 function call 时，系统执行工具，并把工具输出放进下一次 sampling request；只返回 assistant message 时，系统把消息写进历史，然后认为这个 turn 完成。

伪代码大致是：

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

沿着这条路径展开，会经过 `run_sampling_request` 构造本次可见的 `ToolRouter` 和 `ToolCallRuntime`，`handle_output_item_done` 把模型输出分流成工具执行或普通 assistant message，`ToolCallRuntime` 真正调度工具、处理并行限制、取消、abort 输出和工具错误回传。

[openai/codex `run_turn`](https://github.com/openai/codex/blob/main/codex-rs/core/src/session/turn.rs) 是理解整个 turn loop 的入口。Codex 的 Agent Loop 围绕 turn、history、tool router、tool runtime、context compaction、pending input、stop hook 和 rollout persistence 组织；工具调用只是其中一环，UI 展示的是用户视角，看不到 runtime 的事件链。

## 停止条件

Agent Loop 必须能停，但它不是靠一句 prompt 让模型“觉得差不多了就停”。真正决定继续还是结束的是运行时，而不是模型自己的承诺。

一次 turn 的常见结构是：模型给出一个结果，运行时检查这个结果后决定下一步。模型要求调用工具时，Codex 执行工具、把结果写回对话历史、再让模型看着新结果继续；工具调用本身不是结束点，它只是下一轮输入的来源。

也有些情况暂时不能结束：用户中途追加输入要纳入 pending input；上下文快满时 Codex 先尝试压缩而不是直接停；stop hook 可能在模型已经给完答案后把额外约束或收尾写回历史，让模型继续。有些停止不是“任务完成”，而是“不能继续”：用户中断时停当前 turn 并取消正在跑的工具；遇到权限边界时发审批请求；上下文溢出、用量限制或不可重试错误时发错误事件并结束 turn。

Codex 的停止机制不是简单的“最多跑 N 步”。它是事件驱动的 turn loop：有工具结果要回灌就继续，有新用户输入就继续，需要压缩就先压缩，需要审批就等待审批，遇到中断或错误就停。自己写 Agent Loop 时可以加最大轮数做保险，但那不是这段主流程的核心设计。

## 失败模式

- **没有预算和停止条件**：Agent 会消耗 token、重复搜索、反复修改同一处文件，甚至在任务完成后继续行动。把“继续”视为需要证明的选择，而不是默认动作。
- **工具错误当普通文本**：错误应该结构化（`permission_denied` / `timeout` / `not_found` / `schema_invalid` / `rate_limited`），不同错误对应不同策略。
- **状态只存在上下文里**：上下文会被截断、摘要和重排。真正重要的状态应该落在代码数据结构或持久化存储里，否则长任务中断后系统无法知道已经做过什么。
- **trace 粒度太粗**：只记录最终回答没有调试价值。至少要记录每轮的模型决策、工具参数、工具结果、错误类别、重试次数和停止原因；涉及敏感数据时要脱敏。
- **把单 loop 没做好归到多 Agent**：很多任务的瓶颈不是 Agent 数量不够，而是单个 loop 的状态、工具、权限和 eval 没做好。[[Single Agent vs Multi-Agent]] 应该先在单 loop 内解决问题。

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
- [openai/codex `run_turn`](https://github.com/openai/codex/blob/main/codex-rs/core/src/session/turn.rs)：看真实 coding agent 如何组织 turn loop。
- [SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering](https://arxiv.org/abs/2405.15793)：用 coding agent 例子理解工具界面和执行反馈如何影响 Agent Loop 表现。
