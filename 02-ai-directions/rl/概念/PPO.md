# PPO

PPO，全称 Proximal Policy Optimization，是一种常用的 policy gradient 方法。它在传统 RL 和早期 RLHF 里都很常见。

它解决的核心工程问题是：更新 policy 时不能一步迈太大，否则训练会不稳定。

## 工作机制

PPO 会用当前 policy 采样轨迹，估计 advantage，然后更新 policy。但更新时会限制新旧 policy 的差异，避免一次更新把策略推坏。

简化流程：

```text
collect rollouts
estimate advantages
optimize clipped objective
evaluate
repeat
```

## 工程形态

在代码里你会看到：

- rollout buffer。
- value function。
- advantage estimation。
- policy loss。
- value loss。
- entropy bonus。
- clipping ratio。

CleanRL 的单文件实现适合读这些细节。Stable-Baselines3 适合快速跑 baseline。

## 参考资料

- [PPO paper](https://arxiv.org/abs/1707.06347)
- [CleanRL](https://github.com/vwxyzjn/cleanrl)
- [Stable-Baselines3 PPO](https://stable-baselines3.readthedocs.io/)
- [[RLHF]]
