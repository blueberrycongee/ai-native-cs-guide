# AI Infra 信息来源

AI Infra 的信息源要优先看一手资料。二手博客可以用来发现问题，但不能直接当结论，因为推理框架和部署栈变化很快。

## 官方文档

- [vLLM docs](https://docs.vllm.ai/en/latest/)：看 [[Inference Serving]]、OpenAI-compatible server、metrics、production stack。先跑 quickstart，再看 serving 和 deployment。
- [SGLang docs](https://docs.sglang.ai/)：看结构化输出、router/model gateway、RadixAttention、metrics 和 tracing。适合和 vLLM 对比。
- [Ray Serve docs](https://docs.ray.io/en/latest/serve/index.html)：看可组合模型服务、autoscaling、Serve LLM。适合理解模型服务和业务逻辑混合部署。
- [KServe docs](https://kserve.github.io/kserve/)：看 Kubernetes 上的 InferenceService、ServingRuntime、generative inference。适合已经有 K8s 基础的人。
- [llama.cpp](https://github.com/ggml-org/llama.cpp)：看本地推理、量化、GGUF、server。适合理解低门槛本地部署。

## 论文和技术报告

- [vLLM paper](https://arxiv.org/abs/2309.06180)：看 PagedAttention 为什么影响 KV cache 管理和吞吐。
- [SGLang paper](https://arxiv.org/abs/2312.07104)：看结构化语言模型程序和 runtime 如何配合。
- [Taming Throughput-Latency Tradeoff in LLM Inference with Sarathi-Serve](https://arxiv.org/abs/2403.02310)：看 prefill/decode 调度为什么影响延迟。

论文不用一开始推公式。先抓问题：请求为什么排队，KV cache 为什么占显存，吞吐和首 token 延迟为什么互相拉扯。

## Issue、Release 和 Benchmark

读 issue 时重点看：

- streaming、tool calling、OpenAI compatibility 是否有坑。
- 某个模型或硬件组合是否稳定。
- metrics 和 tracing 是否能支撑线上排查。
- 多 LoRA、多模型、长上下文是否有真实限制。

release note 要看 breaking changes。推理服务升级后，API 行为、默认参数和模型支持可能会变。

## 社区讨论

HN、Reddit、Discord 和 GitHub Discussions 适合发现真实踩坑，例如：

- 单卡能跑，不代表并发能扛住。
- OpenAI-compatible 端点之间行为不完全一致。
- 长上下文请求会压垮 KV cache。
- 自部署省不省钱，取决于流量形态和运维成本。

社区讨论只能当线索。最后要回到官方文档、源码、example 和自己的测试。
