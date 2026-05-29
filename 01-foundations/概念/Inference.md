# Inference

Inference 是模型已经训练好之后，根据输入生成输出的过程。你调用 LLM API、部署本地模型、做 streaming response，本质上都在使用推理。

相关概念：

- [[Transformer]]
- [[Token]]
- [[Context Window]]
- [[Streaming Response]]
- [[AI Infra]]

## 学到什么程度

入门阶段要知道：

- 推理不是训练，它使用已有模型参数生成结果
- 输出通常是一个 token 接一个 token 生成
- latency、throughput、context length 和成本会互相影响
- temperature、top_p 这类参数会影响采样，但不能修复任务设计问题

暂时可以跳过：

- CUDA kernel 和显存布局
- KV cache 的具体实现
- speculative decoding、continuous batching 等推理优化细节

做 [[AI Infra]] 时再深入这些内容。做应用时，先能解释为什么模型响应慢、为什么流式输出有价值、为什么超时和取消很重要。

## 在项目里怎么出现

常见场景：

- 后端调用模型 API，等待首 token 和完整输出
- 前端用 [[SSE]] 或 [[WebSocket]] 展示流式结果
- 长 prompt 导致首 token 变慢
- 并发请求上来后，成本和限流开始变成问题

如果你做的是产品，而不是单次 demo，推理体验很快会变成工程问题。

## 资料

- [OpenAI API docs](https://platform.openai.com/docs)：看模型调用、streaming 和参数说明。
- [vLLM docs](https://docs.vllm.ai/)：了解开源推理服务的工程问题。
