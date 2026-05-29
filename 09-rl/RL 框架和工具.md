# RL 框架和工具

RL 工具要按阶段选。先用小环境和可读实现建立概念，再用可靠库做 baseline，最后才看分布式框架。

## 必须会用

- Gymnasium：能跑环境，能写自定义环境。
- Stable-Baselines3：能训练、保存、加载和评测一个 baseline。
- CleanRL：能读一个单文件算法实现，知道 rollout、advantage、loss 在哪里。

## 必须理解

- RLlib：理解 env runner、learner、多 agent、offline RL，但不必一开始使用。
- TensorBoard 或 Weights & Biases：记录 reward、loss、episode length、eval metrics。
- PettingZoo：多 agent 环境接口。
- TRL/OpenRLHF：理解大模型 post-training 里的 RL 路径。

## 只需知道存在

- Brax、MuJoCo、Isaac Gym：物理仿真环境。
- CORL：offline RL 单文件实现。
- OpenSpiel：博弈和多 agent 研究环境。

## 学习顺序

1. Gymnasium basic usage。
2. Stable-Baselines3 训练 PPO。
3. CleanRL 读 PPO 单文件实现。
4. 修改 reward，观察策略变化。
5. 再读 RLlib 或 TRL/OpenRLHF。

工具不是重点。重点是你能解释环境、奖励和策略为什么产生当前行为。
