# Security

AI 系统里的 Security 不只是传统 Web 安全。模型会处理用户输入、私有数据和工具调用，风险会从文本生成扩展到真实动作。

最重要的一点：prompt 不是安全边界。只要系统能读文件、查数据库、发请求、发邮件或执行代码，就必须在代码和基础设施层做权限控制。

## 风险来源

常见风险包括：

- API key 泄露。
- 用户数据进入不该进入的 prompt。
- [[RAG]] 召回了别人的文档。
- 工具调用缺少权限检查。
- prompt injection 诱导 [[Agent]] 忽略规则。
- 日志保存了 secret、隐私或内部资料。

这些问题不能靠一句“不要泄露信息”解决。模型会处理不可信输入，检索资料也可能包含恶意指令。

## 工程形态

AI 应用至少要有几层防线：

- [[API Auth]]：用户身份、模型 key 和工具凭证分开。
- 数据隔离：检索、日志和文件访问按用户或租户过滤。
- 工具白名单：模型只能请求允许的工具。
- 参数校验：工具参数由代码检查，不由模型说了算。
- 人类确认：高风险动作执行前暂停。
- 审计日志：记录谁在什么上下文下触发了什么动作。

Agent 系统尤其要小心。模型生成的工具调用只是建议，最终执行权必须在代码里。

## 最小例子

```python
def execute_tool(user, tool_call):
    tool = registry.get(tool_call.name)
    if tool is None:
        raise ToolNotAllowed(tool_call.name)
    if not policy.allow(user, tool, tool_call.args):
        raise PermissionDenied(tool_call.name)
    return tool.run(validated_args(tool_call.args))
```

这段代码比“请不要调用危险工具”的 prompt 更重要。

## 边界和失败模式

常见失败包括：

- API key 放进前端环境变量。
- RAG 只做向量相似度，不做租户过滤。
- Agent 能读取整个文件系统，却没有工作目录限制。
- prompt injection 出现在网页、PDF 或工具返回里，被当成高优先级指令。
- 日志为了调试记录完整 prompt，长期保存用户隐私。

安全不是阻止 AI 做事，而是让系统知道哪些事不能由模型直接决定。

## 参考资料

- [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/)：LLM 应用风险分类。
- [OWASP API Security Top 10](https://owasp.org/API-Security/)：API 层基础风险。
- [[Tool Use]]：工具调用为什么必须有边界。
