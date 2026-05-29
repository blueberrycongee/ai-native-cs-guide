# AI Infra

AI Infra 是让模型训练、推理、评测和应用稳定运行的基础设施。

它不是单纯“会部署模型”。更现实的工作包括：推理服务、GPU 利用率、数据管道、模型评测、日志与 tracing、成本控制、权限和安全边界。

相关概念：

- [[Inference]]
- [[Context Window]]
- [[Prompt Cache]]
- [[Evals]]
- [[API Auth]]
- [[Streaming Response]]

## 为什么值得学

AI 项目从 demo 走向可用系统时，问题很快从“模型能不能回答”变成：

- 响应太慢怎么办
- 并发上来后成本怎么控
- 模型输出失败怎么定位
- 工具调用和用户数据怎么隔离
- 版本升级后质量有没有退化

这些都属于 AI Infra 的范围。

## 入门边界

入门阶段先学：

- LLM API 调用、超时、重试和限流
- streaming response 的前后端链路
- 基本 eval 和日志
- prompt、模型版本和数据集的版本管理
- 推理成本和 token 预算

先不要急着自建训练平台。没有真实负载时，平台化容易变成空架子。

## 项目方向

可以从这些小项目开始：

- 做一个支持 [[SSE]] 的模型流式输出服务
- 给一个 [[Agent]] 项目加日志、重试和 eval
- 比较不同模型在同一任务上的延迟、成本和质量
- 用 [[Prompt Cache]] 优化长上下文任务

更完整的路径入口见 [[AI Infra 路径]]。

## 资料

- [vLLM docs](https://docs.vllm.ai/)：看推理服务常见工程问题。
- [OpenTelemetry](https://opentelemetry.io/docs/)：理解 tracing 和可观测性。
- [OpenAI Evals GitHub](https://github.com/openai/evals)：了解 eval 在工程里的基本形态。
