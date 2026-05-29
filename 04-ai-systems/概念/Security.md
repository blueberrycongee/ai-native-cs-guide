# Security

AI 系统里的 Security 不只是传统 Web 安全。模型会处理用户输入、私有数据和工具调用，风险会从文本生成扩展到真实动作。

相关概念：

- [[API Auth]]
- [[Tool Use]]
- [[Agent]]
- [[RAG]]
- [[Context Engineering]]

## 为什么值得学

最常见的风险并不神秘：

- API key 泄露
- 用户数据进入不该进入的 prompt
- RAG 泄露别人的文档
- 工具调用缺少权限检查
- prompt injection 诱导 Agent 忽略规则或调用危险工具

这些问题很难靠“写个更强的 system prompt”解决。需要权限、隔离、审计和确认机制。

## 学到什么程度

入门阶段先掌握：

- 前端不能持有供应商 API key
- 用户输入和检索资料都不可信
- 工具调用必须在代码层做权限检查
- 高风险动作要有人类确认
- 日志里不要泄露 secret 和隐私数据

暂时不必做完整安全审计。但只要项目能访问文件、网络、数据库或第三方账号，就应该把安全当成系统设计的一部分。

## 资料

- [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/)：看 LLM 应用常见风险。
- [OWASP API Security Top 10](https://owasp.org/API-Security/)：看 API 层基础风险。
