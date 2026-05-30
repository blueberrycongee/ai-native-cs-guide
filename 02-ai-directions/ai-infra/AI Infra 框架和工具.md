# AI Infra 框架和工具

AI Infra 的工具很多，别按工具名堆学习路线。先按问题分组：模型怎么服务、请求怎么路由、系统怎么观察、成本怎么控制。

## 必须会用

- vLLM：会启动 OpenAI-compatible server，会发送普通和 streaming 请求，会看 metrics。
- 一个 HTTP 后端框架：能写 [[Model Gateway]]，处理鉴权、超时、取消和错误分类。
- OpenTelemetry 或等价 tracing 工具：能把一次请求串起来。
- Prometheus/Grafana 或等价 metrics 栈：能看延迟、错误率、吞吐、队列和资源。

这一级要求能动手，不是知道名字。

## 必须理解

- SGLang：理解结构化输出、runtime、router/model gateway 的位置。
- Ray Serve：理解模型服务和业务逻辑组合部署。
- KServe：理解 Kubernetes 上的模型服务抽象。
- llama.cpp：理解本地推理、量化、GGUF 和硬件限制。
- LiteLLM 或同类网关：理解多供应商模型代理、fallback、budget 和 API 统一。

这些工具不一定都要在第一个项目里跑，但要知道它们解决的问题。

## 只需知道存在

- Triton Inference Server：更通用的模型服务，适合多框架推理。
- TensorRT-LLM：NVIDIA 生态里的高性能 LLM 推理优化。
- DeepSpeed-Inference、Text Generation Inference：不同生态里的推理服务。
- Kubernetes HPA/KEDA：做弹性扩缩容时会遇到。
- verl/OpenRLHF：当 AI Infra 延伸到大模型 RL 后训练时，要理解 rollout engine、trainer、reward service、Ray worker、sequence packing 和 eval pipeline。详细见 [[LLM RL Infra]]。

## 学习顺序

1. 先写自己的最小模型网关，理解 [[HTTP]]、[[SSE]]、[[API Auth]] 和日志。
2. 接 vLLM，观察自部署推理和托管 API 的差异。
3. 加 metrics 和 trace，补 [[Observability]]。
4. 需要多模型或多供应商时，再做 [[Model Routing]]。
5. 需要集群和弹性时，再看 Ray Serve 或 KServe。

工具不是目标。目标是能解释一次请求为什么慢、为什么失败、为什么贵，以及改动后质量有没有退化。
