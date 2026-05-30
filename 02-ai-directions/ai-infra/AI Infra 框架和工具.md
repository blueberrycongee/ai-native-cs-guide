# AI Infra 框架和工具

AI Infra 的工具可以按问题域来看：模型如何服务，请求如何路由，系统如何观测，成本如何控制。

## 推理服务

- vLLM：OpenAI-compatible server、continuous batching、KV cache、metrics。
- SGLang：structured outputs、runtime、router/model gateway、RadixAttention。
- llama.cpp：本地推理、GGUF、量化、CPU/GPU offload、`llama-server`。
- Text Generation Inference：Hugging Face 生态里的推理服务。
- TensorRT-LLM：NVIDIA 生态里的高性能 LLM 推理优化。
- Triton Inference Server：通用模型服务，覆盖多框架推理。

## 模型网关和路由

- LiteLLM：多供应商模型代理、fallback、budget 和统一 API。
- 自建 HTTP 后端：封装 [[Model Gateway]]、鉴权、超时、取消、错误分类和流式事件。
- SGLang router / model gateway：多后端路由、metrics 和 tracing。

## 观测和评测

- OpenTelemetry：trace、span 和跨服务请求链路。
- Prometheus / Grafana：延迟、错误率、吞吐、队列和资源监控。
- 结构化日志：trace id、用户、模型、token、延迟、错误类型和模型版本。
- eval pipeline：把模型版本、prompt 版本、上下文策略和结果质量关联起来。

## 部署和扩缩容

- Ray Serve：Python 模型服务、deployment graph、autoscaling 和 Serve LLM。
- KServe：Kubernetes 上的 InferenceService、ServingRuntime 和 generative inference。
- Kubernetes HPA / KEDA：弹性扩缩容。

## 与后训练系统的交叉

- verl / OpenRLHF：当 AI Infra 延伸到大模型 RL 后训练时，会出现 rollout engine、trainer、reward service、Ray worker、sequence packing 和 eval pipeline。相关系统视角见 [[LLM RL Infra]]。
