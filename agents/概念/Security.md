# Security

Security 是 Agent 项目绕不开的部分。

Agent 比普通 chatbot 风险更高，因为它可能拥有工具、文件系统、浏览器、消息平台、API 和支付相关权限。

先关注：

- 工具权限最小化
- 高风险操作需要确认
- 不可信输入可能诱导 Agent 做错事
- 第三方 skills 和插件可能带来供应链风险
- secrets 不应该进入 prompt、日志或公开仓库

相关概念：

- [[Tool Use]]
- [[Skills]]
- [[Human in the Loop]]
- [[Evals]]
