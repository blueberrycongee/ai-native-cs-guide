# Cost

Cost 是 AI 项目能不能长期运行的基本约束。模型调用、embedding、向量检索、推理部署、日志存储和 [[Evals]] 都会产生成本。

很多 demo 看起来能跑，一到真实用户就撑不住。问题通常不是“模型太贵”这么简单，而是系统没有预算意识。

## 成本来自哪里

常见成本项包括：

- 输入 token 和输出 token。
- embedding 和索引重建。
- 向量数据库、对象存储和日志。
- Agent 多轮循环和工具调用。
- 自部署推理的 GPU、队列和空闲资源。
- eval 样例集的定期运行。

成本和延迟经常绑在一起。长 prompt 不只贵，也会让首 token 更慢。输出过长不只贵，也会占用连接和 worker。

## 工程形态

一个项目至少要记录：

```text
request_id
user_id or org_id
model
input_tokens
output_tokens
latency_ms
number_of_model_calls
number_of_tool_calls
cache_hit
error_type
```

没有这些字段，就无法知道是某个用户、某个 prompt、某个工具还是某个模型版本在烧钱。

## 控制方式

常见控制点：

- 限制每个任务的最大模型调用轮数。
- 对 [[RAG]] 检索结果做 top-k、压缩和重排。
- 为长上下文使用 [[Prompt Cache]]。
- 用小模型处理分类、改写和路由。
- 对失败重试设置上限和退避。
- 对免费用户和高成本工具做配额。

成本控制不是最后的优化项。它会反过来决定产品是否能开放给真实用户。

## 边界和失败模式

常见失败包括：

- Agent 没有循环上限。
- 检索返回太多 chunk，模型每次都读一堆无关内容。
- 重试策略把 429 和 500 都当成可以立即重试。
- 只记录总账单，不记录单次请求成本。
- 自部署只看 GPU 利用率，不看排队、p95 延迟和空闲成本。

一个可上线项目至少要能回答：一次典型任务多少钱，最坏情况多少钱，谁在使用，失败时是否仍然计费。

## 参考资料

- [OpenAI pricing](https://openai.com/api/pricing/)：托管模型计费入口。
- [vLLM production stack](https://docs.vllm.ai/en/v0.21.0/deployment/integrations/production-stack/)：自部署推理的生产栈入口。
- [[Prompt Cache]]：重复上下文的成本优化。
