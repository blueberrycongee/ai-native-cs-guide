# Chunking

Chunking 是把原始文档切成检索单元。它不是简单的“每 500 个字切一下”。chunk 决定了 RAG 能召回什么，也决定了模型看到的证据是否完整。

## Chunk 是检索单元

RAG 里模型最后看到的不是完整文档，而是若干个 chunk。一个 chunk 太小，会丢掉标题、定义对象、表格上下文和前后条件；太大，又会让相似度被噪声稀释，并浪费上下文窗口。

所以 chunk 的目标不是平均长度，而是让每个检索单元在被单独拿出来时仍然能回答一个局部问题。

## 切块策略

常见策略包括：

- fixed-size chunk：按 token 长度切，简单但容易切断语义。
- recursive chunk：按标题、段落、句子、token 长度逐层切。
- semantic chunk：按主题变化或 embedding 相似度切。
- parent-child chunk：用小 chunk 检索，用大段落或父文档提供上下文。
- contextual chunk：给 chunk 添加它在原文中的简短背景说明。

没有通用最佳 chunk size。法律条款、API 文档、论文、代码库、客服 FAQ 的最佳切法都不同。

## Metadata 比 overlap 更重要

很多人靠 overlap 缓解上下文丢失，但更关键的是 metadata：

- `doc_id`
- `chunk_id`
- 标题层级
- 段落位置
- 页码或行号
- 更新时间
- 权限和租户
- 原始 URL 或文件路径

没有这些信息，后续 [[Citation]]、[[Metadata Filtering]]、增量更新和故障排查都会变困难。

## 常见失败

- 按字符切块，导致中英文、代码和表格被切坏。
- 去掉标题层级，chunk 里出现“它”“本条款”“该接口”但不知道指什么。
- PDF 解析错误，页眉页脚、水印和断行进入 chunk。
- chunk 太小，召回了相关句子但无法支持完整答案。
- chunk 太大，reranker 和模型都被无关内容干扰。

## 验收

评测 chunk 策略时，不要只问最终答案。先看 expected source 是否进入 top-k，再看引用是否支持答案。chunk 策略如果让 citation support 变差，就算最终答案偶尔对了，也不能算稳定。

## 相关概念

- [[RAG]]
- [[RAG 评测]]
- [[Citation]]
- [[Context Window]]
