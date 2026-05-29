# Embedding

Embedding 是把文本、图片或其他对象表示成向量的方法。对初学者来说，先把它理解成“方便机器比较语义相似度的坐标”就够了。

它和 [[Token]]、[[Transformer]]、[[RAG]] 都有关。语言模型内部会使用向量表示，应用层也常用 embedding 做检索、推荐和去重。

相关概念：

- [[Token]]
- [[RAG]]
- [[Context Engineering]]
- [[AI Infra]]

## 学到什么程度

入门阶段需要知道：

- embedding 不是原文，它是向量表示
- 相似向量通常表示语义上更接近
- embedding 适合做召回，不等于事实判断
- 向量数据库只是存储和检索 embedding 的一种工程组件

暂时可以跳过：

- embedding 模型训练细节
- 向量索引算法的完整实现
- 每种距离度量的数学证明

等你真的需要优化召回质量或向量检索性能，再补这些更划算。

## 在项目里怎么出现

最常见的项目是 [[RAG]]。流程通常是：

1. 把文档切块
2. 用 embedding 模型把每块变成向量
3. 用户提问时，把问题也变成向量
4. 找出相似文档块，放进模型上下文

这里最容易出问题的不是“有没有向量数据库”，而是文档切块、召回质量、排序和答案校验。

## 资料

- [OpenAI Embeddings guide](https://platform.openai.com/docs/guides/embeddings)：看 embedding 在应用里的基本用法。
- [FAISS GitHub](https://github.com/facebookresearch/faiss)：了解向量检索库，不必一开始读完整源码。
