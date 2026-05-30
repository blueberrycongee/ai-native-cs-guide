# AI Infra 开源项目

AI Infra 项目可以按推理引擎、结构化生成、服务编排、Kubernetes 模型服务和本地推理几个形态来整理。

## vLLM

- GitHub：[vllm-project/vllm](https://github.com/vllm-project/vllm)
- Docs：[vLLM docs](https://docs.vllm.ai/en/latest/)
- 类型：高吞吐 LLM 推理引擎
- 代表形态：OpenAI-compatible server、continuous batching、PagedAttention、KV cache 管理和 production metrics。
- 相关主题：[[Inference Serving]]、TTFT、tokens/s、p95 latency、GPU memory、长上下文。

## SGLang

- GitHub：[sgl-project/sglang](https://github.com/sgl-project/sglang)
- Docs：[SGLang docs](https://docs.sglang.ai/)
- 类型：结构化生成和高性能推理 runtime
- 代表形态：结构化输出、前端语言、runtime、router/model gateway 和多后端路由。
- 相关主题：JSON schema、regex、grammar、RadixAttention、metrics、tracing、[[Model Routing]]。

## Ray Serve

- GitHub：[ray-project/ray](https://github.com/ray-project/ray)
- Docs：[Ray Serve](https://docs.ray.io/en/latest/serve/index.html)
- 类型：可扩缩模型服务和 Python 业务逻辑部署
- 代表形态：把 prompt 处理、检索、模型调用和后处理放进同一条服务链路。
- 相关主题：Serve LLM、autoscaling、deployment graph、LLM examples。

## KServe

- GitHub：[kserve/kserve](https://github.com/kserve/kserve)
- Docs：[KServe docs](https://kserve.github.io/kserve/)
- 类型：Kubernetes 原生模型服务平台
- 代表形态：InferenceService、ServingRuntime、autoscaling、canary 和模型服务标准化。
- 相关主题：generative inference、Hugging Face runtime、vLLM runtime、模型存储。

## llama.cpp

- GitHub：[ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)
- 类型：本地推理和量化生态
- 代表形态：消费级硬件上的本地推理、GGUF、quantization、`llama-server` 和 CPU/GPU offload。
- 相关主题：本地部署、量化、显存占用、吞吐和硬件限制。
