# ReAct

ReAct 来自论文 [ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629)。这篇论文要解决的问题不是提出一个完整的 Agent 工程架构，而是把大语言模型的两类能力放到同一个任务轨迹里：一类是语言推理，也就是 reasoning trace；另一类是面向环境的 task-specific action。

论文的核心观点是：只让模型推理，容易停留在内部知识里，出现幻觉和错误传播；只让模型行动，又缺少对目标、状态和异常的显式跟踪。ReAct 让模型在同一条轨迹里交错生成 thought 和 action，使 reasoning 帮助选择行动，也使行动返回的 observation 反过来支撑后续 reasoning。

## 论文里的形式化设定

论文先把任务写成一个 agent 和环境交互的问题。第 `t` 步时，agent 接收环境 observation，并基于当前上下文选择 action。传统 action space 记作 `A`。

ReAct 的做法是把 action space 扩展成：

```text
A_hat = A union L
```

其中 `L` 是语言空间。也就是说，论文把 thought / reasoning trace 看成一种特殊的“语言动作”。这种语言动作不会改变外部环境，也不会得到新的 observation；它的作用是把当前上下文里的信息组织起来，更新模型后续决策可用的轨迹上下文。

这点很重要。ReAct 不是简单地说“每一步都先想再做”，而是在原来的环境动作之外，允许模型插入自由形式的语言推理。thought 可以用来分解目标、制定计划、提取 observation 里的关键信息、补充常识、追踪进度、处理异常、调整下一步搜索或行动。

## Thought、Action、Observation

在常见展示里，ReAct 轨迹长这样：

```text
Thought: ...
Action: ...
Observation: ...
Thought: ...
Action: ...
Observation: ...
```

但这个格式不要理解成固定模板。论文里区分了两类任务：

- 在 HotpotQA 和 FEVER 这类知识密集推理任务里，轨迹通常是 dense thought，也就是多个 thought-action-observation 步骤交替出现。
- 在 ALFWorld 和 WebShop 这类长 horizon 决策任务里，thought 是 sparse 的，不需要每个 action 前都想一次，而是让模型在关键位置决定是否生成 thought。

所以忠于论文的说法应该是：ReAct 让模型在任务轨迹中交错生成 reasoning traces 和 task-specific actions。它可以表现为 Thought/Action/Observation 的循环，但不等于所有任务都必须机械套这个循环。

## 论文做了哪些实验

论文评估了四类任务：

- HotpotQA：多跳问答。
- FEVER：事实验证。
- ALFWorld：文本版家居环境决策任务。
- WebShop：网页购物导航任务。

在 HotpotQA 和 FEVER 里，作者给模型一个很简单的 Wikipedia API。动作空间只有三类：

- `search[entity]`：搜索实体页面。
- `lookup[string]`：在当前页面里查找字符串，类似浏览器里的 Ctrl+F。
- `finish[answer]`：结束任务并提交答案。

这个设计不是为了做最强检索器。论文明确说这个 API 比真实检索系统弱，目的反而是逼模型通过显式语言推理来决定查什么、怎么改写查询、什么时候结束。

在 ALFWorld 里，模型要通过文本动作在模拟家居环境里完成任务，比如找物品、打开容器、放置物品。这里 thought 主要用于拆解目标、追踪子目标、判断下一步子目标，以及用常识判断物品可能在哪里。

在 WebShop 里，模型要根据用户购物需求搜索商品、浏览页面、选择选项并购买目标商品。这里行动空间更接近真实网页交互，文本观察也更嘈杂。

## ReAct 和 CoT、Act-only 的关系

论文比较了几种方法：

- Standard：直接回答或直接预测。
- CoT：只有 reasoning，没有 action 和 observation。
- Act：只有 action 和 observation，没有 thought。
- ReAct：reasoning 和 action 交错出现。

论文的结论不是“ReAct 永远比 CoT 好”。更准确的结论是：

- ReAct 一般比 Act-only 更好，因为 thought 能帮助模型决定下一步行动，尤其是合成最终答案。
- ReAct 比 CoT 更 grounded，因为它能通过外部环境获得事实信息，所以在一些任务上减少幻觉。
- CoT 在组织纯推理结构上有时更灵活。论文在 HotpotQA 上观察到，ReAct 因为受 action/observation 结构约束，推理灵活性可能下降。
- 在 HotpotQA 和 FEVER 上，论文里表现最好的往往是 ReAct 和 CoT-SC 的组合，而不是单独 ReAct。

这也是读 ReAct 时最容易被讲歪的地方。ReAct 的贡献不是取代 CoT，而是说明外部行动和内部语言推理可以互补：action 提供 grounded information，thought 负责利用这些信息推进任务。

## 论文里的失败模式

论文自己列出了 ReAct 的几个限制，不能只讲它的优点。

第一，ReAct 会受检索结果质量影响。HotpotQA 里，非信息性的 search result 会让模型后续 reasoning 偏掉，而且模型不一定能恢复。

第二，ReAct 也会出现 reasoning error。论文提到一个常见模式：模型重复生成之前的 thought 和 action，跳不出循环。这不是现代 Agent 系统才有的问题，原论文实验里已经观察到了。

第三，ReAct 的结构约束会降低某些推理任务的灵活性。它更 grounded，但不总是更会组织复杂推理。

第四，复杂动作空间需要更多示例。论文最后也指出，动作空间大的复杂任务可能需要更多 demonstrations，而这会撞上 in-context learning 的上下文长度限制。

第五，外部 action space 有安全风险。论文的实验刻意把动作限制在 Wikipedia、WebShop benchmark 这类低风险环境里，没有让模型真的编辑网页、购买商品或访问隐私信息。

## 和今天 Agent 工程的关系

今天很多 Agent 文档把 ReAct 讲成“Agent Loop = Thought -> Action -> Observation”。这个说法有启发性，但不够严谨。

忠于论文，ReAct 首先是一个 prompt-based paradigm：用少量人工写的任务轨迹，让冻结 LLM 学会在推理和行动之间切换。它不是工具权限系统，不是状态持久化系统，也不是生产级调度器。

现代工程里的 ReAct，已经从一种 prompt 格式，变成了一种运行时控制循环。重点变化不在于 thought 是否展示给用户，而在于系统不再把 `Thought:` 当作核心协议。

原论文的核心结构可以简化成：

```text
Thought -> Action -> Observation
```

现代工程里的核心结构更接近：

```text
model decides -> tool call -> runtime executes -> observation/state update -> model continues
```

也就是说，现代系统继承的是“根据外部观察继续决策”的思想；真正的工程边界通常变成结构化工具调用、运行时状态、权限检查、trace 和停止条件。reasoning trace 仍然可以用于计划、解释、进度说明或调试，但系统不能把安全、权限、停止和状态恢复寄托在模型写出来的 `Thought` 文本上。

把 ReAct 迁移到工程系统时，应该保留论文里的两个核心点：

- reasoning trace 用来组织上下文、计划、异常和进度，而不是装饰性地写一段“我在思考”。
- action 必须接入外部环境并返回 observation，否则 ReAct 就退化成普通 CoT。

至于权限、日志、超时、重试、停止条件、trace 存储和 eval，这些是 [[Agent Loop]] 和 [[Tool Use]] 的工程问题。它们可以受 ReAct 启发，但不能说是 ReAct 论文本身已经解决的问题。

## 参考资料

- [ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629)：原始论文。
- [ReAct project site](https://react-lm.github.io/)：作者项目页，包含论文、代码和示例说明。
- [[Agent Loop]]
- [[Tool Use]]
