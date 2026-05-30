# Environment

Environment 是 RL 里智能体互动的世界。它接收 action，返回 observation、reward 和终止信息。

没有清楚的环境，就没有可训练的 RL 问题。很多失败项目不是算法差，而是环境定义含糊。

## 工作机制

Gymnasium 风格接口是：

```python
obs, info = env.reset()
obs, reward, terminated, truncated, info = env.step(action)
```

observation 是智能体看到的状态。action 是智能体能做的动作。reward 是环境给出的反馈。terminated/truncated 表示 episode 为什么结束。

## 工程形态

一个可用环境要定义：

- observation space。
- action space。
- reward function。
- termination condition。
- reset 逻辑。
- 随机种子和可复现性。

大模型 agent 环境也类似。代码任务、网页任务、工具任务，本质上都要定义状态、动作和反馈。

## 常见失败模式

- observation 泄露答案，训练分数虚高。
- action space 太大，探索无效。
- termination 条件不清，episode 变成无限循环。
- 环境随机性不可控，实验不可复现。
- reward 和真实目标不一致。

## 参考资料

- [Gymnasium docs](https://gymnasium.farama.org/)
- [[Reward]]
- [[Policy]]
