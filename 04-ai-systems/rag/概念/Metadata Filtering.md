# Metadata Filtering

Metadata Filtering 是根据文档元数据过滤检索结果。它不是可选优化，而是 RAG 的权限、版本和业务边界。

## 为什么 metadata 重要

向量相似度只回答“像不像”，不回答“能不能看”“是不是最新”“是不是这个产品版本”“是不是当前租户的数据”。

常见 metadata 包括：

- `tenant_id`
- `user_id` 或权限组
- `doc_id`
- `source_type`
- `product`
- `version`
- `created_at`
- `updated_at`
- `valid_from` / `valid_to`
- `language`
- `security_level`

这些字段要在索引时写入，而不是查询时临时猜。

## 过滤位置

权限过滤应该尽量发生在检索阶段或检索前，而不是把无权内容召回后再让模型“不要说”。模型不应该看到用户无权访问的证据。

典型流程：

```text
query
  -> build permission filter
  -> retrieve with metadata filter
  -> rerank allowed candidates
  -> generate answer
```

Qdrant 这类向量库支持 payload 和 payload index，用来在向量检索时结合字段过滤。无论使用哪种存储，核心原则相同：权限和版本要进入检索条件。

## 常见失败

- 多租户系统只按向量相似度召回，导致跨租户泄露。
- 文档更新后新旧 chunk 同时可见。
- 产品版本没有过滤，答案混合了 v1 和 v2。
- 删除文档只删原文，不删索引。
- metadata 只用于显示 citation，不参与检索。

## 验收

RAG 评测集里必须有权限和版本样例：

- 用户 A 不能召回用户 B 的文档。
- v2 问题不能引用 v1 文档。
- 删除或过期文档不能被召回。
- citation 中能显示来源版本和更新时间。

## 相关概念

- [[RAG]]
- [[Citation]]
- [[Security]]
- [[API Auth]]
