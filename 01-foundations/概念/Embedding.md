# Embedding

Embedding 是把离散对象变成向量的方式。对象可以是 [[Token]]、句子、文档、图片或用户行为。向量本身没有神秘感，它只是让模型可以用数值计算相似度、聚类和检索。

在大语言模型里，token id 会先查 embedding 表，变成向量后进入 [[Transformer]]。在 [[RAG]] 里，文档片段和查询会被编码成向量，再用向量数据库或搜索库找相近内容。

## 两种常见用法

第一种是模型内部 embedding。它属于模型参数的一部分，负责把 token id 变成模型能处理的向量。

第二种是检索 embedding。它通常由单独的 embedding 模型生成，目标是让语义相近的文本在向量空间里距离更近。RAG 系统主要用这一类。

这两种 embedding 不要混在一起。拿生成模型内部的 token embedding 去做文档检索，通常不是正确路径。

## 工程形态

一个典型 RAG 索引流程是：

```text
documents -> clean -> chunk -> embed -> store vectors + metadata
query -> embed -> vector search -> rerank/filter -> prompt context
```

这里每一步都会影响最终答案。embedding 质量不好，后面的 prompt 再漂亮也很难补回来。chunk 太粗会召回噪声，chunk 太细会丢上下文。metadata 没设计好，多租户隔离、时间过滤和权限过滤都会变麻烦。

## 最小例子

概念上，向量检索就是找余弦相似度高的片段：

```python
query_vec = embed(query)
results = vector_store.search(
    vector=query_vec,
    top_k=8,
    filter={"user_id": current_user.id},
)
```

真实项目里还要保存原文、来源、段落位置、权限信息和索引版本。向量库不是事实来源，它更像可重建的索引。

## 边界和失败模式

Embedding 常见失败点：

- 只用向量相似度，关键词、数字、代码符号和专有名词召回差。
- 换了 embedding 模型却没重建索引。
- 文档更新后只追加向量，旧片段仍被召回。
- 多用户数据共用集合但没有 metadata 过滤，造成权限泄露。
- 用相似度分数当成答案可信度。它只能说明“像”，不能说明“真”。

因此生产 RAG 往往会做 hybrid search、rerank、引用检查和评测。向量只是入口，不是完整答案。

## 参考项目和资料

- [Sentence Transformers](https://www.sbert.net/)：常用开源 embedding 模型与训练工具。
- [Qdrant documentation](https://qdrant.tech/documentation/)：看 collection、payload filter、hybrid search。
- [Milvus documentation](https://milvus.io/docs)：适合了解向量数据库的部署和索引概念。
- [[RAG]]：embedding 在系统里的位置。
