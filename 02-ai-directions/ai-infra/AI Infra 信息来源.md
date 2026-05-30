# AI Infra 信息来源

AI Infra 的资料入口主要围绕推理服务、模型网关、部署平台、性能论文和社区问题展开。推理框架和部署栈变化很快，release note、issue 和 benchmark 往往和文档同样重要。

## 官方文档

- [vLLM docs](https://docs.vllm.ai/en/latest/)：[[Inference Serving]]、OpenAI-compatible server、metrics、production stack。
- [SGLang docs](https://docs.sglang.ai/)：结构化输出、router/model gateway、RadixAttention、metrics 和 tracing。
- [Ray Serve docs](https://docs.ray.io/en/latest/serve/index.html)：可组合模型服务、autoscaling、Serve LLM。
- [KServe docs](https://kserve.github.io/kserve/)：Kubernetes 上的 InferenceService、ServingRuntime、generative inference。
- [llama.cpp](https://github.com/ggml-org/llama.cpp)：本地推理、量化、GGUF、server。

## 论文和技术报告

- [vLLM paper](https://arxiv.org/abs/2309.06180)：PagedAttention、KV cache 管理和吞吐。
- [SGLang paper](https://arxiv.org/abs/2312.07104)：结构化语言模型程序和 runtime 协同。
- [Taming Throughput-Latency Tradeoff in LLM Inference with Sarathi-Serve](https://arxiv.org/abs/2403.02310)：prefill/decode 调度和延迟吞吐权衡。

## Issue、Release 和 Benchmark

- streaming、tool calling、OpenAI compatibility 的实现差异。
- 模型、硬件和长上下文组合下的稳定性。
- metrics、tracing 和 production stack 的线上排查能力。
- 多 LoRA、多模型、长上下文和 GPU memory 的真实限制。
- release note 里的 breaking changes、默认参数变化和模型支持变化。

## 社区讨论

HN、Reddit、Discord 和 GitHub Discussions 里经常出现部署细节：单卡能跑和并发能扛住是两回事，OpenAI-compatible 端点之间行为不完全一致，长上下文请求会压垮 KV cache，自部署成本取决于流量形态和运维成本。
