# Tool Use

Tool Use 是让模型调用外部能力的方法。工具可以是搜索、文件读写、数据库查询、浏览器操作、代码执行、发邮件或业务 API。

没有工具，[[Agent]] 只能生成文本。有工具后，它能改变外部世界，所以系统边界必须更清楚。

## 工作机制

常见流程是：

```text
tool schema -> model chooses tool + args -> code validates -> tool executes -> result returns to context
```

模型只负责提出工具调用。是否允许执行、参数是否有效、结果如何截断，应该由代码决定。

## 工程形态

一个工具至少需要：

- name：稳定名称。
- description：什么时候使用。
- input schema：参数类型和约束。
- permission policy：谁能用、能访问哪些资源。
- result contract：返回什么，错误怎么表示。

工具结果不能无限塞回上下文。长结果要摘要，敏感结果要脱敏，失败结果要结构化。

## 最小例子

```json
{
  "name": "read_file",
  "input": {
    "path": "docs/Agent.md"
  }
}
```

执行前代码要检查：路径是否在允许目录内，用户是否有权限，文件是否过大，结果是否需要截断。

## 常见失败模式

下面几条是 Tool Use 经常被讲到的工程失败，多发在 production 阶段而不是 demo 阶段：

- **description 写得泛**：模型按 description 选 tool；描述里没有触发条件、边界、否定条件，模型会按字面意图匹配，把不该用的工具也调起来。这种问题通常不会在 happy path 暴露，要靠专门设计的反例 eval 才能发现。
- **schema 校验在 prompt 里、不在代码里**：把“路径必须在 allowlist 内、SQL 必须是 SELECT、参数必须满足类型”写在 system prompt 里，模型会绕过去。把这些约束做成工具执行前的代码检查，才能稳定挡住危险输入。
- **错误信息是自然语言**：`Tool returned an error` 对模型来说和 `permission_denied` / `timeout` / `rate_limited` 是不同的。结构化错误让模型可以走不同的恢复路径：换参数、缩小任务、等待重试、停止。
- **结果回写没有截断**：长 stack trace、长 diff、长日志直接拼回上下文，会挤掉对话历史和后续观察。工具结果应有明确的长度上限和摘要策略。
- **高风险动作没有审批流**：写文件、删资源、发外部请求、调支付或消息类 API，应该在执行前进入 [[Security]] 的审批通道，而不是让模型自己决定。

工具调用是 Agent 从“说”到“做”的分界线。这里不能靠运气。

## 参考资料

- [OpenAI function calling guide](https://platform.openai.com/docs/guides/function-calling)：看工具调用的基本形态。
- [Model Context Protocol](https://modelcontextprotocol.io/docs)：了解工具和上下文接入的一种标准化方向。
- [[Security]]：工具权限和 prompt injection 风险。
