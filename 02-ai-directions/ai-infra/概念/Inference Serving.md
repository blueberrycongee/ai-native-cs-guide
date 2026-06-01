# Inference Serving

Inference Serving 是把训练好的模型变成可被应用调用的在线服务。它不是简单地 `model.generate()` 包一层 HTTP。真实服务要处理并发、排队、显存、KV cache、streaming、错误、升级和观测。

## 核心问题

LLM 推理通常分两个阶段：

- prefill：处理输入上下文，建立 KV cache。
- decode：一个 token 一个 token 生成输出。

prefill 更像一次大计算，decode 更像长时间的小步循环。服务端要同时处理很多请求，就必须在吞吐、首 token 延迟、显存和公平性之间取舍。

## 工程形态

一个推理服务通常包含：

```text
HTTP/OpenAI-compatible API
  -> tokenizer
  -> scheduler
  -> model workers
  -> KV cache manager
  -> streaming response
  -> metrics/logs
```

vLLM、SGLang、llama.cpp 都可以提供 OpenAI-compatible API，但内部调度和能力不同。兼容接口不等于兼容行为。

## 最小实验

先用 vLLM 或 SGLang 跑一个小模型：

- 发送非流式和流式请求。
- 记录 TTFT、总延迟、输出 token 数。
- 同时发 1、4、16 个请求，看排队和延迟变化。
- 改变输入长度，看 prefill 对首 token 的影响。

这个实验比只跑一个 demo 更有价值，因为它能让你看到服务形态。

## 常见失败模式

- 长上下文请求占满 KV cache，短请求也变慢。
- streaming 接口能跑，但反向代理或框架缓冲导致前端看不到流式。
- OpenAI-compatible server 对 tool call 或 structured output 支持不一致，工程上要按后端分别测一遍。

## 参考项目

- [vLLM quickstart](https://docs.vllm.ai/en/latest/getting_started/quickstart.html)
- [SGLang docs](https://docs.sglang.ai/)
- [llama.cpp server](https://github.com/ggml-org/llama.cpp)
- [[Model Gateway]]
- [[GPU Basics]]
