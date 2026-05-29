# Transformer

Transformer 是现代大语言模型最重要的基础结构。你不需要一开始就推公式，但要知道它为什么适合处理语言、代码和长序列。

先把它理解成一种用 [[Attention]] 在一段输入里分配注意力的神经网络结构。早期序列模型常按顺序处理 token，Transformer 更适合并行训练，也更容易扩展到大模型规模。

相关概念：

- [[Attention]]
- [[Token]]
- [[Embedding]]
- [[Context Window]]
- [[Inference]]

## 学到什么程度

入门阶段够用的理解是：

- 文本会先被切成 [[Token]]
- token 会变成向量，也就是 [[Embedding]]
- 模型通过 attention 判断当前 token 应该参考上下文里的哪些位置
- 训练阶段学参数，[[Inference]] 阶段根据已有上下文继续预测下一个 token

暂时不必深挖的内容：

- 多头注意力的矩阵推导
- layer norm、残差连接、位置编码的变体细节
- MoE、FlashAttention、KV cache 的底层实现

这些细节不是不重要，只是它们更适合在你读模型推理系统、训练框架或论文复现时再补。

## 对哪些方向重要

几乎所有方向都会遇到 Transformer，但需要的深度不同。

做 [[Agent]]、[[RAG]] 或 AI 应用开发时，知道输入如何变成 token、上下文为什么有限、模型为什么会按概率生成内容，已经能解释很多工程问题。

做 [[AI Infra]]、[[Pre-Train]]、[[Post-Train]] 或 [[RL]] 时，需要进一步理解训练、推理、显存、吞吐和延迟之间的关系。那时再回来看 Transformer 细节会更有意义。

## 在项目里怎么出现

你通常不会手写一个 Transformer。更常见的是间接遇到它：

- 调模型 API 时，输入输出按 token 计费
- 上下文太长时，模型变慢或成本上升
- 做 [[RAG]] 时，需要决定哪些内容放进上下文
- 做推理部署时，需要理解 batch、KV cache 和显存占用

如果一个项目说自己“用了大模型”，最终大概率还是会碰到这些问题。

## 资料

- [Attention Is All You Need](https://arxiv.org/abs/1706.03762)：原始论文。先看摘要、模型结构图和结论，不必一开始推完整公式。
- [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)：适合建立直觉。
- [Hugging Face Transformers docs](https://huggingface.co/docs/transformers)：适合了解模型如何在工程里被调用。
