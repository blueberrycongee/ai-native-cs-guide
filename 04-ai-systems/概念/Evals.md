# Evals

Evals 是用来判断模型、prompt、RAG 或 Agent 是否真的变好的测试方法。

没有 eval，很多 AI 项目的“效果变好”只是感觉。模型升级、prompt 调整、检索策略变化，都可能让一部分案例变好，另一部分案例退化。

相关概念：

- [[LLM]]
- [[RAG]]
- [[Agent]]
- [[Post-Train]]
- [[RL]]

## 为什么值得学

AI 系统不像普通函数那样稳定。它会受模型版本、上下文、采样参数、检索结果和工具状态影响。

Evals 至少帮你回答：

- 改动后哪些样例变好了
- 哪些样例退化了
- 错误来自检索、生成还是工具调用
- 当前结果能不能上线给真实用户

## 学到什么程度

入门阶段先做小而具体的 eval：

- 收集真实任务样例
- 定义可判断的成功标准
- 保留输入、输出、引用和错误原因
- 每次模型或 prompt 改动后重跑

不必一开始搭复杂平台。一个稳定的样例集和清晰判断标准，价值比漂亮 dashboard 更高。

## 在项目里怎么出现

常见 eval：

- [[RAG]] 答案是否引用了正确资料
- [[Agent]] 是否完成任务并避免危险工具调用
- 结构化输出是否能被 JSON parser 接受
- 模型升级后成本、延迟和质量是否可接受

## 资料

- [OpenAI Evals GitHub](https://github.com/openai/evals)：了解 eval 数据和运行方式。
- [LangSmith evaluation docs](https://docs.smith.langchain.com/evaluation)：看应用层 eval 工作流。
