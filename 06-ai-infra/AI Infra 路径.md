# AI Infra 路径

AI Infra 关注 AI 系统能不能稳定、可观测、可控成本地运行。它不等于“会把模型部署起来”，更接近把模型调用、推理服务、评测、日志、安全和成本做成工程系统。

## 学习顺序

1. 先读 [[Inference]]、[[HTTP]]、[[Streaming Response]]、[[SSE]]。
2. 做模型调用网关，记录 token、延迟、错误和 trace。
3. 加入 [[API Auth]]、超时、取消、重试和配额。
4. 用 [[Evals]] 比较模型版本、prompt 版本和上下文策略。
5. 读 [[Inference Serving]]、[[Model Gateway]]、[[Observability]]、[[Model Routing]] 和 [[GPU Basics]]。
6. 再看自部署推理：vLLM、SGLang、Ray Serve、KServe。

没有真实流量前，不要先搭复杂平台。先把一个小服务做稳。

## 必做项目

必做项目是模型调用网关。最小版本见 [[项目路线]]。

功能边界：

- 后端封装模型 API，前端不接触供应商 key。
- 支持普通响应和 [[SSE]] 流式响应。
- 记录首 token 延迟、完整响应延迟、输入输出 token、模型版本和错误类型。
- 用户取消后能停止上游请求。
- 对 401、429、5xx、timeout 做不同处理。

进阶版本可以接 vLLM OpenAI-compatible server，把托管模型和自部署模型放到同一个网关后面。

## 参考项目

- [vLLM](https://docs.vllm.ai/en/latest/)：代表高吞吐 LLM 推理服务。先看 OpenAI-compatible server、serving、production stack。
- [SGLang](https://docs.sglang.ai/)：代表结构化生成和高性能 runtime。先看 quickstart、router/model gateway、structured outputs。
- [Ray Serve](https://docs.ray.io/en/latest/serve/index.html)：代表可组合、可扩缩的 Python 模型服务。先看 Serve 和 Serve LLM。
- [KServe](https://kserve.github.io/kserve/)：代表 Kubernetes 上的模型服务抽象。先看 InferenceService、ServingRuntime 和 generative inference。
- [llama.cpp](https://github.com/ggml-org/llama.cpp)：代表本地推理、量化和 GGUF 生态。先看 server、quantization 和 examples。

更详细的项目阅读入口见 [[AI Infra 开源项目]]。工具分层见 [[AI Infra 框架和工具]]。信息跟踪方式见 [[AI Infra 信息来源]]。

## 工程实践清单

- 请求日志包含 trace id、用户、模型、token、延迟、错误。
- 流式输出能穿过框架和代理，不被缓冲。
- 重试有上限，不对不可重试错误重复烧钱。
- 模型版本、prompt 版本和 eval 结果能关联。
- API key、用户身份和工具凭证分层管理。
- 自部署服务观察 queue time、TTFT、tokens/s、p95 latency、GPU memory。

## 必学知识

必须能写代码或读配置：

- [[HTTP]]
- [[SSE]]
- [[Streaming Response]]
- [[API Auth]]
- [[Inference]]
- [[Cost]]

必须理解机制：

- [[Token]]
- [[Context Window]]
- [[Prompt Cache]]
- [[Evals]]
- [[Security]]

知道存在即可，等需要再深入：

- 分布式训练平台
- Kubernetes operator
- GPU 调度细节
- 多节点推理并行策略

## 阶段验收标准

- 能解释一次模型请求从浏览器到模型再回浏览器的完整链路。
- 能用日志定位慢在排队、prefill、decode、代理还是前端渲染。
- 能证明用户取消后上游请求被关闭。
- 能比较两个模型在同一 eval 集上的质量、延迟和成本。
- 能说明自部署推理和托管 API 的成本、运维、弹性和安全差异。

## 继续深入

下一步按问题选：

- 延迟和吞吐：读 vLLM、SGLang。
- 服务编排：读 Ray Serve、KServe。
- 本地部署：读 llama.cpp。
- 质量回归：加 [[Evals]] 和 tracing。
- 成本失控：补 [[Prompt Cache]]、配额和模型路由。
