# AI Infra 开源项目

AI Infra 项目可以按推理引擎、结构化生成、服务编排、Kubernetes 模型服务和本地推理几个方向来看。

## vLLM

- GitHub：[vllm-project/vllm](https://github.com/vllm-project/vllm)
- Docs：[vLLM docs](https://docs.vllm.ai/en/latest/)

vLLM 是高吞吐 LLM 推理引擎，常见形态是 OpenAI-compatible server、continuous batching、PagedAttention、KV cache 管理和 production metrics。它和 [[Inference Serving]]、TTFT、tokens/s、p95 latency、GPU memory、长上下文这些问题直接相关。

## SGLang

- GitHub：[sgl-project/sglang](https://github.com/sgl-project/sglang)
- Docs：[SGLang docs](https://docs.sglang.ai/)

SGLang 关注结构化生成和高性能推理 runtime。它把结构化输出、前端语言、runtime、router/model gateway 和多后端路由放在一起，也会遇到 JSON schema、regex、grammar、RadixAttention、metrics、tracing 和 [[Model Routing]]。

## Ray Serve

- GitHub：[ray-project/ray](https://github.com/ray-project/ray)
- Docs：[Ray Serve](https://docs.ray.io/en/latest/serve/index.html)

Ray Serve 是可扩缩模型服务和 Python 业务逻辑部署框架。很多 AI 应用会把 prompt 处理、检索、模型调用和后处理放进同一条服务链路，Ray Serve 的 Serve LLM、autoscaling、deployment graph 和 LLM examples 都围绕这类问题展开。

## KServe

- GitHub：[kserve/kserve](https://github.com/kserve/kserve)
- Docs：[KServe docs](https://kserve.github.io/kserve/)

KServe 是 Kubernetes 原生模型服务平台，围绕 InferenceService、ServingRuntime、autoscaling、canary 和模型服务标准化展开。generative inference、Hugging Face runtime、vLLM runtime 和模型存储是它在 LLM 服务里常见的交叉点。

## llama.cpp

- GitHub：[ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)

llama.cpp 代表本地推理和量化生态。它把消费级硬件上的本地推理、GGUF、quantization、`llama-server` 和 CPU/GPU offload 放在一起，适合观察本地部署里的显存占用、吞吐和硬件限制。
