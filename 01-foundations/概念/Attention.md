# Attention

Attention 解决的问题很直接：模型处理当前位置时，应该看上下文里的哪些内容。

在 [[Transformer]] 里，attention 不是“像人一样注意”，而是一套计算权重的方法。每个 token 会和其他 token 建立关系，模型再根据这些关系组合信息。

相关概念：

- [[Transformer]]
- [[Token]]
- [[Context Window]]
- [[Inference]]

## 学到什么程度

入门阶段要掌握三件事：

- attention 让模型能在上下文里找相关信息
- 上下文越长，计算和显存压力通常越大
- attention 权重不是可靠解释工具，不能简单当作“模型真正的理由”

暂时可以跳过：

- query、key、value 的完整矩阵推导
- 多头注意力每个 head 学到了什么
- 各种稀疏注意力、线性注意力和长上下文优化

如果你以后做 [[AI Infra]] 或模型训练，这些会变重要。做应用时，先理解它对上下文、成本和延迟的影响。

## 在项目里怎么出现

做 AI 应用时，attention 通常不会直接出现在代码里，但会影响很多设计：

- prompt 太长，模型变慢
- 放进上下文的资料太多，关键信息反而被冲淡
- [[RAG]] 检索结果排序不好，模型可能引用不该引用的内容
- 长文档处理需要切块、摘要或多轮检索

所以 attention 的工程意义不是背公式，而是知道模型并不会平等、稳定地使用所有上下文。

## 资料

- [Attention Is All You Need](https://arxiv.org/abs/1706.03762)：看第 3 节即可建立基本结构。
- [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)：图示对初学者更友好。
