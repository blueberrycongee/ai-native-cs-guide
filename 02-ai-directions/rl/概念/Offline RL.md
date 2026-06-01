# Offline RL

Offline RL 是只用已有数据训练策略，不再和环境实时交互。它适合真实环境昂贵、危险或不可随便试错的场景。

它听起来很适合现实问题，但难点也很现实：数据里没覆盖的动作，模型不该自信地乱试。

## 工作机制

输入是一批历史轨迹：

```text
(observation, action, reward, next_observation)
```

算法要从这些数据里学 policy。问题是，训练数据通常只覆盖一小部分状态和动作。策略一旦选择数据分布外动作，估计会很不可靠。

## 工程形态

Offline RL 项目要记录：

- 数据从哪里来。
- 行为策略是什么。
- 数据覆盖哪些状态和动作。
- reward 是否可靠。
- eval 是否能在真实或模拟环境里验证。

大模型场景里，很多"从历史轨迹学习 agent 行为"的问题都能借用 Offline RL 的风险视角。

## 参考资料

- [RLlib offline RL docs](https://docs.ray.io/en/master/rllib/index.html)
- [CORL](https://github.com/tinkoff-ai/CORL)
- [[Policy]]
- [[Reward]]
