# Tool Use

Tool Use 是让模型调用外部能力的方法。工具可以是搜索、文件读写、数据库查询、浏览器操作、代码执行、发邮件或调用业务 API。

没有工具，[[Agent]] 只能生成文本。有工具后，它能改变外部世界，所以系统设计要更谨慎。

相关概念：

- [[Agent]]
- [[Agent Loop]]
- [[API Auth]]
- [[Security]]
- [[Context Engineering]]

## 为什么重要

Tool use 把模型从“回答问题”推进到“执行任务”。这也是风险变大的地方。

你需要关心：

- 模型能调用哪些工具
- 每个工具的输入 schema 是否清楚
- 工具执行前是否需要确认
- 工具失败时如何返回错误
- 工具结果是否要被摘要或截断再放回上下文

如果这些边界不清楚，Agent 很容易做出看似合理但不可控的动作。

## 学到什么程度

入门阶段先做两个工具就够：

- 一个只读工具，比如搜索文件或查询资料
- 一个低风险写工具，比如创建草稿或生成报告

先不要让模型直接执行高风险操作。删除文件、发生产邮件、改数据库、花钱调用服务，都应该有权限和确认机制。

## 在代码里怎么出现

常见实现方式：

- 用 JSON schema 描述工具参数
- 模型输出工具名和参数
- 程序校验参数
- 程序执行工具
- 工具结果回到模型上下文

注意：参数校验和权限检查必须在代码里做，不能只靠 prompt。

## 资料

- [OpenAI Function Calling guide](https://platform.openai.com/docs/guides/function-calling)：看工具调用的基本形态。
- [Model Context Protocol](https://modelcontextprotocol.io/docs)：了解工具和上下文接入的一种标准化方向。
