# Hermes Agent Memory

Hermes Agent 的 memory 不是一套统一的长期记忆系统，而是两条机制并行：

- 内置文件记忆：`MEMORY.md`（Agent 自己的笔记）和 `USER.md`（用户画像），通过 `memory` 工具维护，文件内容会渲染进 system prompt。
- 外部 memory provider：通过 `MemoryProvider` 接口接入 Mem0、Honcho、Hindsight 等后端，负责跨 session 的抽取、检索、用户建模。

两条路径都叫 memory，但工程位置不同。内置文件更像小而稳定的 prompt bootstrap 层；外部 provider 更像跨 session 的召回和建模层。两者都不是“无限长期记忆”，它们各管一段时效、容量和信任关系。

## 内置文件记忆：frozen snapshot

`MemoryStore` 管理两个 profile-scoped 的文件：

- `MEMORY.md`：Agent 对环境、工具、项目约定的笔记。
- `USER.md`：用户偏好、沟通方式、长期约束。

这里的设计核心是 frozen snapshot。Agent 启动时从磁盘加载文件，把内容渲染成 system prompt 里的一块固定文本（`_system_prompt_snapshot`），并长期缓存。session 进行中，模型如果通过 `memory` 工具写入新条目，文件会立即落盘，但当前 session 的 system prompt 不会跟着更新——工具返回的是 live state，prompt 用的是启动时的 snapshot。

这套设计把 memory 分成两个时间尺度：

- 当前 session：prompt 前缀保持稳定，利于 provider prefix cache。
- 之后的 session：重新加载磁盘，新的 memory 才进入 system prompt。

唯一的例外是 context compression。压缩会触发 `invalidate_system_prompt()`，重新从磁盘 `load_from_disk()`，所以压缩后的新 session 能看到本轮已经写入的 memory。

这个设计的取舍很直接：内置文件记忆牺牲“实时反映写入”的能力，换 prompt 的稳定性、容量可控和 prefix cache 友好。它不适合做大规模历史召回，只适合放少量、高信任、需要稳定进入 system prompt 的事实。

## 进入 prompt 的路径

内置 memory 进入模型上下文的方式是直接渲染，不是检索或按需 read。`MemoryStore` 加载文件后切成条目、跑一遍安全扫描，再生成 snapshot；构造 system prompt 时，snapshot 渲染成 volatile 块。两个开关独立控制：

- `memory_enabled` 打开时，`MEMORY.md` 进入 prompt。
- `user_profile_enabled` 打开时，`USER.md` 进入 prompt。

`memory` 工具在这里是写入和查看通道，不是读取通道——它返回 live state，模型要看自己写的 memory 不需要再调一次 read 工具，因为 snapshot 已经在 prompt 里了。区分这点很重要：把 read 工具误当成内置 memory 的入口，会把整个设计读反。

## 写入的边界

Hermes 对内置 memory 写什么收得很窄。适合写的是用户偏好、环境事实、工具怪癖、稳定约定；不适合写的是任务进度、PR / issue / commit 之类的临时信息、PR 号、issue 号、commit SHA、阶段记录，以及一次性的 TODO。判断标准也直接：能减少用户之后重复纠正、重复说明次数的信息才进 memory。

更关键的是 fact 和 instruction 的区分。`User prefers concise responses` 是事实；`Always respond concisely` 在之后的 session 里会变成一条强指令，可能盖过当前用户请求。Hermes 明确把 memory 收成 declarative facts，规则是写在 `MEMORY_GUIDANCE` 里的，不是模型自己读出来的隐式偏好。

## 文件层防护

内容一旦进入 system prompt，风险就接近 context file，而不是普通日志。Hermes 在写入前会跑 threat pattern 扫描；命中注入或外泄类模式时，写入被拒绝。启动时也会扫描已有条目；可疑条目不会进入 prompt snapshot，但保留在 live state，方便后续查看和删除。

写文件用 lock 加 atomic replace。每次 add / replace / remove 前都重新读盘，避免多 session 并发互相覆盖。如果文件被外部 patch、shell append 或手工编辑成不能按 `§` 分隔符稳定 round-trip 的形态，Hermes 先备份再拒绝写入，避免静默丢内容。

这套防护说明内置文件 memory 的目标不是“能存多少”，而是“少而稳、注入可控”。

## 外部 Memory Provider

外部 provider 通过 `MemoryProvider` 接口接入，由 `MemoryManager` 统一调度。插件目录在 `plugins/memory/<name>/`，当前实现包括 Mem0、Honcho、Hindsight、Holographic、RetainDB、ByteRover、Supermemory 等。

`MemoryProvider` 覆盖了一个 Agent turn 的多个位置：初始化、turn 开头、LLM 调用前 prefetch、压缩前提取、turn 完成后 sync、session 结束、session 旋转，以及可选的工具暴露。这个 hook 集合回答的是同一个问题：在 turn 的哪些时机，runtime 应当向 provider 喂数据或取回数据。

`MemoryManager` 负责 fan-out、异常隔离、工具路由和 schema 去重。一个工程细节值得提：内置文件记忆在代码结构上不是作为 `MemoryProvider` 注册进 manager 的——它由 `_memory_store` 和 `memory` 工具单独处理，外部 provider 拿到内置写入是通过显式 bridge 调 `on_memory_write()`。所以“内置和外部并行”指的是运行时的两条路径，不是 manager 下的两个 provider。

## Turn 里的数据流

一个普通用户 turn，memory 相关路径大致是：

1. 初始化：`MemoryStore` 加载内置文件，配置了 `memory.provider` 时加载外部 provider。
2. 构造 system prompt：内置文件 snapshot 进入 volatile block；外部 provider 的 `system_prompt_block()` 也进入 volatile block。
3. 用户消息进入 conversation loop 后，先调 `on_turn_start()`，再对原始用户消息调 `prefetch_all()`。
4. prefetch 结果不会写回 session history，而是在 API call 前拼到当前用户消息后面，用 `<memory-context>` 包起来，并加 system note 说明这是 recalled memory context，不是新的用户输入。
5. 工具循环中：内置 `memory` 工具走 `MemoryStore`；provider 自己的工具（`mem0_search`、`hindsight_recall` 等）走 `MemoryManager.handle_tool_call()` 的路由。
6. turn 正常完成后，调 `sync_all()` 写回 provider，再 `queue_prefetch_all()` 为下一 turn 预热；用户中断时外部 sync 跳过，不完整的回答或中断的 stream 不会被当作长期事实写入。
7. `<memory-context>` 是 API-call-time injection，不是持久消息，session DB 里保留的是原始用户消息。

## Context Fencing

外部 provider 返回的召回文本会被包成 `<memory-context>...</memory-context>`，加上 system note 说明这是 recalled memory，不是用户的新输入。两件事由此变得可执行：模型能区分 recalled memory 和当轮输入；runtime 在 streaming 输出层可以识别并 scrub 这类 block，避免 provider context 泄漏到用户可见回复。

注意一个边界：这是上下文隔离和展示层清理，不是权限系统。真正的信任边界仍然在 provider、工具权限、数据发送范围和配置里。把 `<memory-context>` 当安全机制会越过它的职责。

## Session 旋转

外部 provider 经常缓存当前 session id、document id、turn buffer 和预取结果。如果 `/new`、`/reset`、`/resume`、`/branch`、context compression 这些路径没通知 provider，之后的写入可能落到旧 session，prefetch 也可能读到旧 session 的缓存。

`on_session_switch(new_session_id, parent_session_id=old_session_id, reason=...)` 是为这个问题加的。compression 路径会先触发旧 session 的 memory extraction，结束旧 session，创建新 session，再调 `on_session_switch()`。Hindsight 的实现是参考实现：flush 旧 buffer、清空 prefetch、更新 `_session_id`、重新生成 `_document_id`、清空 turn buffer。少一步，后续 session 就会写到旧 document。

## 一个 Provider 例子：Mem0

Mem0 的接法能看出外部 memory 的典型分工：

- `system_prompt_block()` 只放 provider 状态和可用工具提示。
- `sync_turn()` 把 user / assistant turn 交给 Mem0 API 做抽取。
- `queue_prefetch()` 后台搜索相关 memory 并缓存。
- `prefetch()` 在下一 turn 前取出缓存并清空。
- 工具层暴露 `mem0_profile` / `mem0_search` / `mem0_conclude`，读用 `user_id` 过滤，写用 `user_id + agent_id` 归因。
- 连续失败时打开 circuit breaker，避免 provider 故障拖慢主流程。

这类 provider 把“抽取什么、去重什么、怎么语义召回”交给外部系统，runtime 只管三件事：什么时候给它数据、什么时候取回上下文、失败时不能阻塞主对话。

## 设计的取舍

Hermes 的 memory 设计不是把所有历史都塞进长期记忆，而是分层处理：

- 稳定、短小、高频使用的信息进内置文件 memory。
- 大量历史对话保留在 session DB，用 `session_search` 查。
- 语义召回、用户建模、自动抽取交给外部 provider。
- 当前 turn 的召回结果只临时注入，不污染 session history。
- provider 故障按 best-effort 处理，不影响主回答。

这套设计的重点不是“记得越多越好”，而是控制 memory 进入模型上下文的时机、来源和成本。内置文件牺牲容量换稳定和可解释；外部 provider 牺牲一部分可控性换跨 session 召回和自动抽取。

## 相关概念

- [[Memory]]：memory 的总入口，按具体系统分。
- [[Agent 概念]]：Agent 方向的概念地图。
- [[Tool Use]]：memory 写入和 recall 都通过工具执行。
- [[Context Engineering]]：内置 memory snapshot 是 volatile prompt block 的一部分。
- [[Evals]]：memory 改动需要可比较的固定任务。

## 源码入口

- [MemoryStore / memory tool](https://github.com/NousResearch/hermes-agent/blob/main/tools/memory_tool.py)
- [MemoryProvider](https://github.com/NousResearch/hermes-agent/blob/main/agent/memory_provider.py)
- [MemoryManager](https://github.com/NousResearch/hermes-agent/blob/main/agent/memory_manager.py)
