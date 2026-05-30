# Policy

Policy 是智能体根据 observation 选择 action 的策略。它可以是规则、表格、神经网络，也可以是一个大语言模型。

在 RL 里，训练的目标通常是找到能获得更高长期回报的 policy。

## 工作机制

policy 可以是确定性的：

```text
action = policy(observation)
```

也可以是随机的：

```text
action ~ policy(. | observation)
```

随机策略对探索很重要。没有探索，智能体可能永远不知道还有更好的动作。

## 工程形态

在传统 RL 中，policy 往往是神经网络。输入 observation，输出动作分布或动作值。

在 LLM agent 里，policy 可以看成“模型加上 prompt、工具 schema、上下文和采样策略”。这不是严格等价，但有助于理解：系统给模型看到什么，会改变它选择什么动作。

## 常见失败模式

- policy 只在训练环境有效，换环境就崩。
- 训练 reward 高，但行为不符合真实目标。
- 随机性太大，评测结果不稳定。
- 只保存模型权重，不保存环境和预处理配置。

## 参考资料

- [OpenAI Spinning Up](https://spinningup.openai.com/)
- [Stable-Baselines3 docs](https://stable-baselines3.readthedocs.io/)
- [[Environment]]
