# Prompting

Prompting 是给模型组织任务说明、上下文和输出要求的方法。

它不是玄学咒语，也不是把需求写得越长越好。好的 prompt 应该减少歧义，给出必要上下文，并让输出能被后续系统使用。

相关概念：

- [[LLM]]
- [[Context Engineering]]
- [[Context Window]]
- [[RAG]]
- [[Tool Use]]

## 学到什么程度

入门阶段先掌握：

- 明确任务目标和输出格式
- 给模型足够但不过量的上下文
- 把约束写成可检查的要求
- 对复杂任务做拆分，而不是把所有要求塞进一个 prompt
- 用 eval 或样例测试 prompt，而不是凭感觉调

暂时不要沉迷 prompt 模板合集。模板只能提供起点，不能替你理解任务。

## 在项目里怎么出现

常见场景：

- 让模型把用户输入改写成检索查询
- 让模型基于检索结果回答问题
- 让模型输出 JSON 给程序消费
- 让 [[Agent]] 判断下一步该调用哪个工具
- 让模型根据错误信息修复代码

这些场景都需要不同的 prompt 设计。一个通用“万能提示词”很少能稳定解决问题。

## 资料

- [OpenAI Prompt Engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)：看官方建议和示例。
- [Anthropic Prompt Engineering](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview)：对比不同模型供应商的建议。
