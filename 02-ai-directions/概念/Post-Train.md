# Post-Train

Post-Train 是模型预训练之后，让模型更适合人类任务和产品场景的一组方法。

它包括指令微调、偏好优化、对齐、拒答策略、工具使用数据和评测。不要把它简单理解成“微调一下模型”。

相关概念：

- [[Pre-Train]]
- [[RL]]
- [[Evals]]
- [[Prompting]]
- [[Tool Use]]

## 为什么值得学

很多用户感知到的模型差异，不只是预训练能力差异，也来自 post-training。

例如：

- 模型是否愿意按指令输出结构化结果
- 是否会拒绝危险请求
- 是否能稳定调用工具
- 是否在多轮对话里保持任务格式

这些都和 post-training 数据、训练目标和评测有关。

## 入门边界

先理解：

- instruction tuning 解决什么问题
- preference optimization 为什么需要人类或模型反馈
- eval 为什么要和训练目标一起设计
- post-training 不能替代产品侧的权限、安全和校验

暂时不必细抠每个 RLHF 或 DPO 公式。先知道它们试图优化什么，再决定是否深入。

## 项目里怎么出现

你一般不会在普通 AI 应用里直接做 post-training，但会用到它的结果：

- 选择模型时比较指令遵循能力
- 为工具调用构造训练或评测样例
- 做领域模型时考虑微调还是 [[RAG]]
- 设计 eval 数据集来检查模型升级后的变化

## 资料

- [InstructGPT paper](https://arxiv.org/abs/2203.02155)：理解 RLHF 在指令模型里的作用。
- [Direct Preference Optimization](https://arxiv.org/abs/2305.18290)：了解偏好优化的一条经典路线。
