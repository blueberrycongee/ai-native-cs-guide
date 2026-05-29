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

## 边界和失败模式

常见失败包括：

- 工具描述模糊，模型频繁选错工具。
- 参数校验缺失，模型生成危险路径或非法 SQL。
- 高风险动作没有确认。
- 工具错误只返回自然语言，模型无法稳定恢复。
- 工具结果过长，把上下文挤爆。

工具调用是 Agent 从“说”到“做”的分界线。这里不能靠运气。

## 参考资料

- [OpenAI function calling guide](https://platform.openai.com/docs/guides/function-calling)：看工具调用的基本形态。
- [Model Context Protocol](https://modelcontextprotocol.io/docs)：了解工具和上下文接入的一种标准化方向。
- [[Security]]：工具权限和 prompt injection 风险。
