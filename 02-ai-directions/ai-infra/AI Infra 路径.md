# AI Infra 路径

AI Infra 把模型调用、推理服务、观测、成本和安全边界连成一条运行链路。它关心的问题不是“模型能不能返回文本”，而是一次请求在真实系统里如何进入、排队、生成、流式返回、记录、评测和回滚。

## 请求链路

一个常见 AI 应用会经过这些环节：

```text
browser / app
  -> API auth
  -> model gateway
  -> hosted API or inference server
  -> streaming response
  -> logs / metrics / traces
  -> eval and regression checks
```

这条链路里会反复出现 [[HTTP]]、[[SSE]]、[[Streaming Response]]、[[API Auth]]、[[Inference]]、[[Cost]] 和 [[Evals]]。

## 系统组件

- [[Model Gateway]]：统一供应商 API、模型参数、错误类型、流式事件和凭证边界。
- [[Inference Serving]]：处理 batch、KV cache、prefill/decode、吞吐和延迟。
- [[Observability]]：把一次请求的模型、token、延迟、错误、trace 和 eval 结果串起来。
- [[Model Routing]]：在不同模型、供应商或推理后端之间做选择。
- [[GPU Basics]]：解释显存、带宽、并发和利用率对服务成本的影响。

## 代表项目

- [vLLM](https://docs.vllm.ai/en/latest/)：高吞吐 LLM 推理服务，相关主题包括 OpenAI-compatible server、continuous batching、KV cache 和 metrics。
- [SGLang](https://docs.sglang.ai/)：结构化生成和高性能 runtime，相关主题包括 router/model gateway、structured outputs、RadixAttention 和 tracing。
- [Ray Serve](https://docs.ray.io/en/latest/serve/index.html)：可组合的 Python 模型服务，相关主题包括 deployment graph、autoscaling 和业务逻辑编排。
- [KServe](https://kserve.github.io/kserve/)：Kubernetes 上的模型服务抽象，相关主题包括 InferenceService、ServingRuntime 和 generative inference。
- [llama.cpp](https://github.com/ggml-org/llama.cpp)：本地推理和量化生态，相关主题包括 GGUF、quantization、server 和 CPU/GPU offload。

更完整的项目入口见 [[AI Infra 开源项目]]，资料入口见 [[AI Infra 信息来源]]，工具生态见 [[AI Infra 框架和工具]]。
