# Pre-Train 方向

Pre-Train 是模型在大规模数据上学习通用模式的阶段。它解释了很多应用层现象：模型为什么会某些语言和代码风格，为什么某些事实知道得模糊，为什么 prompt 不能凭空造出模型从未学过的能力。

这条方向不适合一开始就冲大规模训练。先用小模型看清 [[Training Loop]]、[[Dataset Pipeline]]、[[Tokenizer Training]]、[[Checkpoint]] 和 eval，再去读 Megatron-LM 这类大规模项目。

## 方向边界

Pre-Train 解决的是“从数据里学通用表示和生成能力”。它不解决：

- 让模型稳定遵循产品格式。
- 给模型接入最新私有知识。
- 让模型安全调用工具。
- 让模型在具体任务上符合用户偏好。

这些通常由 [[Post-Train]]、[[RAG]]、[[Prompting]]、[[Tool Use]] 和产品系统处理。

## 推荐进入顺序

1. 读 [[Transformer]]、[[Token]]、[[Embedding]]。
2. 读 [[Training Loop]]，理解 batch、loss、optimizer 和 generation sample。
3. 用 nanoGPT 跑一个小实验。
4. 读 [[Dataset Pipeline]] 和 [[Tokenizer Training]]，理解数据不是随便丢进去。
5. 用 LitGPT 看更完整的 pretrain、finetune、evaluate 工作流。
6. 最后读 [[Distributed Training]] 和 Megatron-LM。

## 目录入口

- [[Pre-Train 路径]]
- [[Pre-Train 信息来源]]
- [[Pre-Train 开源项目]]
- [[Pre-Train 框架和工具]]
- [[Training Loop]]
- [[Dataset Pipeline]]
- [[Tokenizer Training]]
- [[Checkpoint]]
- [[Distributed Training]]
