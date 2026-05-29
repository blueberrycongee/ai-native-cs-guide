# API Auth

API Auth 处理两个问题：谁能调用接口，以及调用时能访问哪些资源。AI 应用里它还关系到模型 key、用户数据、工具权限和成本控制。

最重要的原则很简单：模型供应商的 API key 不应该出现在浏览器里。前端请求自己的后端，后端再用受控凭证调用模型。

## 工程形态

一个常见结构是：

```text
browser user session
  -> your backend auth
  -> policy check / quota check
  -> model provider API key
```

用户身份和模型供应商 key 是两层东西。用户登录证明“这个人是谁”；供应商 key 证明“你的服务能调用模型”。不要把这两层混起来。

## 需要控制的权限

AI 系统的权限不只在 HTTP 接口上：

- 用户能上传、检索、删除哪些文档。
- [[RAG]] 检索时能看到哪些向量和原文。
- [[Agent]] 能调用哪些工具，工具能读写哪些系统。
- 某个用户或组织的模型调用额度是多少。
- 日志里哪些内容需要脱敏。

特别是 Agent。只要工具能发邮件、改数据库、执行代码或访问内部系统，权限就不能靠 prompt 约束。prompt 不是安全边界。

## 最小例子

后端调用工具前应做显式授权：

```python
def run_tool(user, tool_name, args):
    if not policy.allow(user, tool_name, args):
        raise PermissionDenied(tool_name)
    return tools[tool_name].run(args)
```

模型可以建议调用工具，但最终是否执行，应由代码根据用户、资源、参数和环境决定。

## 边界和失败模式

常见失败包括：

- API key 写进前端环境变量，被打包进静态资源。
- RAG 向量库没有按用户或租户过滤，召回了别人的文档。
- 日志记录完整 prompt，泄露用户隐私和内部资料。
- Agent 工具只看工具名，不校验参数里的资源权限。
- 免费用户没有速率限制，成本被少数请求打爆。

API Auth 不是登录页的附属功能。只要模型能访问数据和工具，它就是系统安全的一部分。

## 参考资料

- [OWASP API Security Top 10](https://owasp.org/API-Security/)：API 风险清单。
- [OpenAI API keys docs](https://platform.openai.com/docs/api-reference/authentication)：模型 API 鉴权方式。
- [[Security]]：AI 系统里的 prompt injection、数据泄露和工具权限。
