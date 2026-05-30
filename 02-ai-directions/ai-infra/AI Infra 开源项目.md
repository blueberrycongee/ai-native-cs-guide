# AI Infra 开源项目

这页列 AI Infra 方向值得研究的真实项目。每个项目都要看它代表的系统形态，不要只看 star。

## vLLM

- GitHub：[vllm-project/vllm](https://github.com/vllm-project/vllm)
- Docs：[vLLM docs](https://docs.vllm.ai/en/latest/)
- 类型：高吞吐 LLM 推理引擎

vLLM 适合学习 [[Inference Serving]]。它的核心学习价值在于 KV cache 管理、continuous batching、OpenAI-compatible server 和 production metrics。

先看：

- Quickstart：跑起 OpenAI-compatible server。
- Serving docs：理解 chat/completions、streaming、metrics。
- 源码入口：server、engine、scheduler、worker 相关目录。
- Issue：看具体模型、硬件和长上下文问题。

不要先读所有底层 CUDA 优化。先跑通服务，记录 TTFT、tokens/s、p95 latency 和显存。

## SGLang

- GitHub：[sgl-project/sglang](https://github.com/sgl-project/sglang)
- Docs：[SGLang docs](https://docs.sglang.ai/)
- 类型：结构化生成和高性能推理 runtime

SGLang 适合学习结构化输出、前端语言和 runtime 协同。它还值得用来观察 router/model gateway、metrics、tracing 和多后端路由。

先看：

- Quickstart：跑单模型服务。
- Structured Outputs：看 JSON schema、regex、grammar。
- Model Gateway：看 [[Model Routing]]、观测和请求传播。
- Paper：理解 RadixAttention 和结构化生成的动机。

## Ray Serve

- GitHub：[ray-project/ray](https://github.com/ray-project/ray)
- Docs：[Ray Serve](https://docs.ray.io/en/latest/serve/index.html)
- 类型：可扩缩模型服务和 Python 业务逻辑部署

Ray Serve 适合学习“模型服务不只是模型”。很多 AI 应用会把 prompt 处理、检索、模型调用、后处理放在同一个服务链路里，Ray Serve 能展示这种组合。

先看：

- Serve quickstart。
- Serve LLM。
- autoscaling 和 deployment graph。
- examples 中包含 LLM 的服务。

## KServe

- GitHub：[kserve/kserve](https://github.com/kserve/kserve)
- Docs：[KServe docs](https://kserve.github.io/kserve/)
- 类型：Kubernetes 原生模型服务平台

KServe 适合已经熟悉 Kubernetes 的读者。它的学习价值在于 InferenceService、ServingRuntime、autoscaling、canary 和模型服务标准化。

先看：

- InferenceService 概念。
- Generative inference 和 Hugging Face/vLLM runtime。
- 示例 YAML。
- Issue 中关于 runtime、autoscaling 和模型存储的问题。

## llama.cpp

- GitHub：[ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)
- 类型：本地推理和量化生态

llama.cpp 适合理解“在消费级硬件上跑模型”会遇到什么。它不是高并发服务的唯一答案，但对量化、GGUF、本地 server、CPU/GPU offload 很有学习价值。

先看：

- README 和 build 文档。
- `llama-server`。
- quantization 文档。
- examples。

## 项目选择建议

第一阶段只需要跑一个托管 API 网关和一个 vLLM/SGLang 本地服务。Ray Serve 和 KServe 等你真的需要服务编排和 Kubernetes 再看。llama.cpp 适合补本地推理和量化直觉。
