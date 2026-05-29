# Prompt Cache

Prompt cache 容易被忽略，但在 Agent 系统里很实际。

Agent 经常会有大量重复上下文，比如：

- 系统提示词
- 工具说明
- skills 内容
- 项目文档
- 长期记忆
- 大段代码或知识库摘要

如果每次请求都重新处理这些内容，成本和延迟都会变高。Prompt cache 要解决的就是这件事：复用相同或稳定的 prompt 前缀，减少重复计算。

看 prompt cache 时要关注：

- 哪些模型和 API 支持
- cache 命中条件是什么
- cache 生命周期多长
- prompt 的哪些部分应该保持稳定
- 动态内容放在哪里才不破坏缓存
- usage 里如何观察 cached tokens

## 资源

- [OpenAI Prompt Caching](https://platform.openai.com/docs/guides/prompt-caching)
- [Anthropic Prompt Caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)

相关概念：

- [[Context Engineering]]
- [[Skills]]
- [[Cost]]
