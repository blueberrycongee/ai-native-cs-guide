# Memory

Memory 是 Agent 保存和使用历史信息的机制。它可以是当前任务状态，也可以是跨会话的长期信息。

先别把 memory 神秘化。很多时候，一个结构化任务状态比复杂向量记忆更有用。

## 工作机制

常见记忆分两类：

- 短期记忆：当前任务目标、已读文件、工具结果、待办步骤、失败原因。
- 长期记忆：用户偏好、项目规则、历史决策、常用路径。

短期记忆通常属于 run state。长期记忆需要权限、隐私、删除和过期策略。

## 工程形态

资料整理 Agent 可以保存：

```json
{
  "read_files": ["README.md", "GOAL.md"],
  "open_questions": ["缺少 AI Infra 项目验收标准"],
  "evidence": [{"file": "GOAL.md", "line": 12}],
  "last_error": null
}
```

这些状态比把全部历史消息塞回 prompt 更可靠，也更容易调试。

## 边界和失败模式

常见失败包括：

- 长期记忆没有来源，错误信息被反复使用。
- 记住用户隐私却没有删除机制。
- 把 memory 当事实来源，不做验证。
- 向量记忆召回相似但不相关的旧信息。
- 任务状态只存在对话里，断线后无法恢复。

入门阶段先做好短期状态、摘要和证据引用。长期记忆晚一点做。

## 参考资料

- [MemGPT paper](https://arxiv.org/abs/2310.08560)：看长期上下文和记忆管理的一种研究路线。
- [LangGraph memory docs](https://docs.langchain.com/oss/python/langgraph/memory)：看 short-term 和 long-term memory 的工程抽象。
