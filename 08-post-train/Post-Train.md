# Post-Train

Post-Train 是模型预训练之后，让模型更适合任务和产品场景的一组方法。它不只是“微调一下”，而是围绕数据、偏好、奖励、评测和推理格式改造模型行为。

这条方向连接 [[Pre-Train]] 和真实应用。预训练给模型底座，post-training 让它更会按指令做事、更稳定输出结构、更懂拒答、更适合工具调用。

## 方向边界

Post-training 主要改变行为，不是补全所有知识。

适合 post-training 的问题：

- 模型不稳定遵循输出格式。
- 模型对特定任务的回答风格不一致。
- 模型需要学习工具调用格式。
- 模型需要在偏好数据下调整回答倾向。

不一定适合 post-training 的问题：

- 最新私有资料问答，优先考虑 [[RAG]]。
- API 权限和安全边界，必须靠系统设计。
- 少量 prompt 能解决的格式问题，不必训练。
- 需要确定性业务规则，应该写代码。

## 推荐进入顺序

1. 读 [[SFT]]，用小模型跑一次指令微调。
2. 读 [[Preference Optimization]] 和 [[DPO]]，理解偏好数据如何进入训练。
3. 补 [[Reward Model]] 和 [[RLHF]]，知道经典 RLHF pipeline 为什么复杂。
4. 读 [[GRPO]]，理解大模型推理类任务里常见的新训练路径。
5. 用同一组 [[Evals]] 比较训练前后，不只看 loss。

## 目录入口

- [[Post-Train 路径]]
- [[Post-Train 信息来源]]
- [[Post-Train 开源项目]]
- [[Post-Train 框架和工具]]
- [[SFT]]
- [[Preference Optimization]]
- [[DPO]]
- [[RLHF]]
- [[GRPO]]
- [[Reward Model]]
