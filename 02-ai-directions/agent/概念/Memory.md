# Memory

Memory 在 Agent 系统里不是一个单一组件。它可能是 prompt bootstrap 文件、运行时 checkpoint、检索索引、用户画像、session 摘要、工具结果缓存，也可能是外部 memory provider。把它抽象成“短期记忆 / 长期记忆”很快会失真。

这页先作为具体实现的入口。

## Hermes Agent Memory

- [[Hermes Agent Memory]]
- [MemoryManager](https://github.com/NousResearch/hermes-agent/blob/main/agent/memory_manager.py)
- [MemoryProvider](https://github.com/NousResearch/hermes-agent/blob/main/agent/memory_provider.py)

Hermes Agent 同时有内置文件记忆和外部 provider 抽象。内置记忆通过 `MEMORY.md` / `USER.md` 进入 system prompt；外部 provider 可以在启动时初始化，在 turn 前 prefetch，在上下文压缩前提取信息，在 turn 后同步写入，也可以向模型暴露 memory 工具。

相关问题集中在 frozen snapshot、provider 生命周期、session scope、外部 provider 数量限制、memory context 注入和清理。

## OpenClaw Memory

- [OpenClaw Memory overview](https://github.com/openclaw/openclaw/blob/main/docs/concepts/memory.md)
- [memory-core](https://github.com/openclaw/openclaw/tree/main/extensions/memory-core)

OpenClaw 的 memory 不像隐藏状态，更像 workspace 里的可读文件层和可插拔检索层。它把 `MEMORY.md`、`memory/YYYY-MM-DD.md`、`DREAMS.md` 放在 agent workspace 里，再通过 active memory plugin 提供 search / get、索引、召回和后端适配。

相关问题集中在文件层、索引层、prompt 注入、daily note 到 durable memory 的蒸馏，以及 Memory Wiki 这种带证据的知识层。

## Mem0

- [mem0ai/mem0](https://github.com/mem0ai/mem0)
- [Mem0 memory types](https://docs.mem0.ai/core-concepts/memory-types)
- [Mem0 memory operations](https://docs.mem0.ai/core-concepts/memory-operations/add)

Mem0 是专门面向 AI Agents 的 memory layer。它把 memory 做成独立系统，围绕 add / search / update / delete、`user_id` / `run_id` 作用域、metadata、LLM 抽取、冲突处理、向量检索和 managed API / OSS 部署组织。

相关问题集中在“从对话里抽取什么”、如何处理重复和冲突、memory 的作用域如何进入检索，以及外部 memory layer 和 Agent runtime 之间的边界。

## LangGraph Memory / Persistence

- [LangGraph memory](https://docs.langchain.com/oss/python/concepts/memory)
- [LangGraph persistence](https://docs.langchain.com/oss/python/langgraph/persistence)

LangGraph 里的 memory 和 persistence 更接近 graph state、checkpoint、store 和 durable execution。它关注一个 agent / workflow 在多个 step、human-in-the-loop、失败恢复和调试过程里如何保存状态。

相关问题集中在 checkpoint、thread state、store、time travel debugging、人类介入和 memory 之间的关系。

## 容易混在一起的东西

同样叫 memory，实际可能指不同对象：

- 当前上下文里的 conversation history。
- Agent loop 的 run state 或 graph checkpoint。
- 用户、组织或 workspace 级别的长期信息。
- 用向量库、全文索引或 hybrid search 做的召回层。
- 日志、trace、工具结果和证据引用。

所以这里按具体项目组织：Hermes Agent Memory、OpenClaw Memory、Mem0、LangGraph Memory，而不是写一篇统一的 Memory 概论。
