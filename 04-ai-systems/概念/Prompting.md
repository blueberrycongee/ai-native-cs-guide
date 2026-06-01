# Prompting

Prompting 是把任务说明、上下文和输出要求组织给模型的方法。它不是咒语，也不是把需求写得越长越好。

好的 prompt 做三件事：减少歧义，提供必要信息，让输出能被后续系统检查或使用。它和 [[Context Engineering]] 的关系是：prompt 负责表达任务，context engineering 负责决定哪些信息进入 [[Context Window]]。

## 工作机制

模型只看到上下文。prompt 里的角色、目标、约束、例子和输出格式，都会改变模型下一步生成 token 的分布。

一个可维护的 prompt 通常会分区：

```text
system rules
task instruction
available context
output contract
examples or counterexamples
```

分区不是为了好看，而是为了调试。输出错误通常来自三个位置：任务说明不清、上下文缺失，或输出契约不够严格。

## 工程形态

Prompting 在项目里常见于：

- 把用户问题改写成检索查询。
- 基于检索结果生成带引用的回答。
- 让模型输出 JSON、SQL 或工具参数。
- 让 [[Agent]] 判断下一步动作。
- 根据错误日志修复代码或配置。

这些场景不能共用一个“万能提示词”。每个 prompt 都应该有输入、输出和失败样例。

## 最小例子

结构化输出场景可以先写成契约：

```text
Return JSON with fields:
- answer: string
- citations: array of source ids
- needs_more_context: boolean

Use only the provided sources. If no source supports the answer,
set needs_more_context to true.
```

然后用代码校验 JSON schema。不要只靠一句“请严格输出 JSON”。

## 边界和失败模式

常见失败包括：

- prompt 里同时要求“简短”和“全面”，模型不知道优先级。
- few-shot 例子和真实输入分布不同。
- 输出格式没有校验，前端或后端解析时崩掉。
- 把安全规则只写进 prompt，没有 [[API Auth]] 和工具权限。
- prompt 改动没有跑 [[Evals]]，只凭一次样例判断变好。

Prompting 是系统设计的一部分，不是替代系统设计。

## 参考资料

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)：看官方建议和示例。
- [Anthropic prompt engineering](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview)：对比不同模型供应商的写法。
- [[Evals]]：prompt 改动应该如何验证。
