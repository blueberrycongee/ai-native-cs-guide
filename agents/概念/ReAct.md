# ReAct

这里的 ReAct 指的是 Reason + Act，不是前端框架 React。

ReAct 的想法是：模型不要只在内部推理，也不要盲目调用工具，而是把推理和行动交替起来。

一个典型形式是：

```text
Thought -> Action -> Observation -> Thought -> Action -> Observation
```

需要知道 ReAct，因为很多 Agent 范式都可以看成它的工程化变体。

## 资源

- [ReAct paper](https://arxiv.org/abs/2210.03629)
- [Google Research blog: ReAct](https://research.google/blog/react-synergizing-reasoning-and-acting-in-language-models/)

相关概念：

- [[Agent Loop]]
- [[Tool Use]]
