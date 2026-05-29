# Prompt Cache

Prompt Cache 是复用重复上下文来降低延迟和成本的机制。它常出现在长 system prompt、长文档、多轮对话和 Agent 工作流里。

相关概念：

- [[Context Window]]
- [[Context Engineering]]
- [[Token]]
- [[Agent]]
- [[Inference]]

## 为什么值得学

长上下文任务很贵，也容易慢。很多请求有重复前缀，比如系统指令、工具说明、代码库摘要、文档片段。

Prompt cache 的价值在于：如果供应商或推理系统能复用这些重复内容，就能减少重复计算。

## 学到什么程度

先理解：

- cache 通常依赖稳定的上下文前缀
- 改动前缀可能导致 cache 失效
- 不同模型供应商的 cache 规则不同
- cache 优化成本和延迟，不保证答案质量变好

暂时不必研究推理引擎内部实现。做应用时，先学会识别哪些上下文应该稳定、哪些内容应该放在后面变化。

## 在项目里怎么出现

常见场景：

- coding agent 每轮都带同一份仓库规则
- 文档问答系统反复带同一批长资料
- 工具调用说明很长，但每次请求都一样
- 多轮任务中系统指令和安全规则固定

这时可以考虑 prompt cache，或者至少把上下文组织成“稳定前缀 + 变化输入”的形态。

## 资料

- [OpenAI Prompt Caching](https://platform.openai.com/docs/guides/prompt-caching)：看自动缓存规则。
- [Anthropic Prompt Caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)：看显式 cache breakpoint。
