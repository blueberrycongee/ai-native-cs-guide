# Evals

Evals 是用来判断模型、prompt、[[RAG]] 或 [[Agent]] 是否真的变好的测试方法。没有 eval，很多“效果变好”只是感觉。

AI 系统不像普通函数一样稳定。模型版本、上下文、采样参数、检索结果和工具状态都会影响输出。Evals 的作用是把这些变化放到一组可重复的任务上比较。

## 工作机制

一个最小 eval 通常包括：

```text
input case
expected evidence or expected behavior
system output
scoring rule
error label
```

评分可以是人工判断、规则检查、LLM-as-judge，或三者组合。不要迷信单一分数。对工程项目来说，错误分类往往比分数更有用。

## 工程形态

RAG eval 可以拆成：

- retrieval：是否找到了正确来源。
- grounding：答案是否被来源支持。
- refusal：证据不足时是否拒答。
- citation：引用是否指向正确片段。

Agent eval 可以拆成：

- task success：任务是否完成。
- tool safety：是否调用了允许的工具。
- recovery：工具失败后是否重试或退出。
- trace quality：人能否看懂每一步为什么发生。

这些指标比“回答看起来不错”更难糊弄。

## 最小项目

先建一个小样例集：

- 20 个正常问题。
- 10 个证据不足的问题。
- 10 个容易混淆的问题。
- 5 个权限或安全相关问题。

每个样例记录输入、期望来源、允许答案、拒答条件和错误标签。每次改 prompt、模型、chunk 策略或工具逻辑，都跑一次。

## 边界和失败模式

常见失败包括：

- 只测自己写的 happy path。
- eval 样例太少，改动只是在过拟合样例。
- LLM-as-judge 没有人审查，judge 也会被上下文误导。
- 只看平均分，不看高风险案例。
- 不保存输入输出和 trace，无法复盘退化原因。

Evals 不会替你定义产品质量，但会逼你说清楚“好”到底是什么。

## 参考项目和资料

- [OpenAI Evals](https://github.com/openai/evals)：看 eval 数据和运行方式。
- [LangSmith evaluation docs](https://docs.smith.langchain.com/evaluation)：看应用层 eval 工作流。
- [Ragas](https://docs.ragas.io/)：看 RAG 评测指标和流程。
