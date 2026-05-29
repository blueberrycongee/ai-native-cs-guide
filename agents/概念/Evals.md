# Evals

Evals 用来评测 Agent 是否真的有用。

只看 demo 很容易误判能力。一个 Agent 项目至少要知道：

- 它在哪些任务上成功
- 它在哪些任务上失败
- 失败是模型问题、工具问题、上下文问题，还是流程问题
- 改 prompt、换模型、加工具之后是否真的变好
- 成本、延迟和稳定性是否可接受

Agent 的 eval 一开始可以很简单，但不能没有。

相关概念：

- [[Agent Loop]]
- [[Tool Use]]
- [[RAG]]
- [[Security]]
- [[Cost]]