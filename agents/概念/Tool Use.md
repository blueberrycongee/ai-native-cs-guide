# Tool Use

Tool use 让 Agent 从“会说话”变成“会做事”。

工具可以是：

- 搜索
- 文件读写
- 数据库查询
- Shell 命令
- 浏览器控制
- API 调用
- 发消息
- 创建 issue / PR

真正要问的是这些问题：

- 工具描述是否清楚
- 参数是否结构化
- 调用前是否需要确认
- 工具失败后如何恢复
- 工具有没有权限边界

## 资源

- [OpenAI Function Calling](https://help.openai.com/en/articles/8555517-function-calling-in-the-openai-api)
- [OpenAI Agents SDK Tools](https://openai.github.io/openai-agents-js/guides/tools)

相关概念：

- [[Agent Loop]]
- [[ReAct]]
- [[Skills]]
- [[Security]]
