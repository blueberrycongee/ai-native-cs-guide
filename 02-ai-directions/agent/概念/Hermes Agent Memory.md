# Hermes Agent Memory

Hermes Agent 的 memory 不是一套单独的长期记忆系统，而是两条机制并行：

- 内置文件记忆：`MEMORY.md` 和 `USER.md`，通过 `memory` 工具维护，并通过 prompt 生效。
- 外部 memory provider：通过 `MemoryProvider` / `MemoryManager` 接入 Mem0、Honcho、Hindsight 等后端。

这两条路径都叫 memory，但工程位置不同。内置记忆更像小而稳定的 prompt bootstrap；外部 provider 更像跨 session 的召回、抽取和建模层。内置文件记忆如果不结合 prompt 看，就只剩下“两个 Markdown 文件”，解释不了它为什么会影响模型行为。

## 内置文件记忆

内置记忆由 `tools/memory_tool.py` 里的 `MemoryStore` 管理。它有两个文件：

- `MEMORY.md`：Agent 自己的笔记，通常放环境事实、项目约定、工具经验。
- `USER.md`：用户画像，通常放偏好、沟通方式、长期约束。

文件位于 profile-scoped 的 `$HERMES_HOME/memories/` 下。默认字符预算在初始化时读配置，常见默认值是 `memory_char_limit=2200`、`user_char_limit=1375`。

这里的核心设计是 frozen snapshot。Hermes 启动 Agent 时从磁盘加载文件，把条目渲染成 system prompt 里的固定块。会话中如果模型通过 `memory` 工具写入新条目，文件会立即落盘，但当前会话的 system prompt 不会跟着变化。工具返回的是 live state，system prompt 用的是启动时 snapshot。

这个设计把 memory 分成两个时间尺度：

- 当前会话：prompt 前缀尽量稳定，利于 provider prefix cache。
- 之后的会话：重新加载磁盘，新的 memory 才进入 system prompt。

context compression 是一个例外。压缩会触发 system prompt invalidation，`invalidate_system_prompt()` 会重新 `load_from_disk()`，所以压缩后的新 session 能看到本轮已经写入的 memory。

## 内置 Memory 和 Prompt

Hermes 的内置 memory 不是“模型有一个记忆区”这么简单。真正起作用的是这条路径：

1. 启动 Agent 时，Hermes 读取 `MEMORY.md` 和 `USER.md`。
2. 这些文件被渲染成 system prompt 里的 memory block。
3. 模型在对话中看到这个 block，于是会被这些长期事实影响。
4. 如果模型调用 `memory` 工具，新条目会写回文件。
5. 下一次 session 启动时，新的文件内容再进入 prompt。

也就是说，文件是存储层，prompt 才是生效层。内置 memory 的设计重点不在“怎么召回大量历史”，而在“哪些少量事实可以稳定地进入 system prompt”。

这条路径里有三块 prompt surface：

| surface | 来源 | 放在哪里 | 作用 |
| --- | --- | --- | --- |
| memory guidance | `agent/prompt_builder.py` 的 `MEMORY_GUIDANCE` | stable system prompt | 说明哪些信息属于长期 memory |
| memory snapshot | `MemoryStore.format_for_system_prompt()` | volatile system prompt block | 把 `MEMORY.md` / `USER.md` 的内容放进上下文 |
| memory tool schema | `tools/memory_tool.py` 的 `MEMORY_SCHEMA` | `memory` 工具描述 | 说明写入、替换、删除 memory 的动作边界 |

`MEMORY_GUIDANCE` 先定义 memory 的范围。它把内置 memory 收得很窄：用户偏好、环境事实、工具怪癖、稳定约定可以进入；任务进度、PR 号、issue 号、commit SHA、阶段完成记录、临时 TODO 不进入。这里还有一个很关键的区分：memory 写的是 declarative facts，不是 self-instruction。`User prefers concise responses` 是事实；`Always respond concisely` 更像一条指令，之后读回 system prompt 时可能压过当前用户请求。

`MEMORY.md` 和 `USER.md` 是第二层。Hermes 不会把文件内容裸贴进 prompt，而是先渲染成带 header、usage 和分隔符的 block。`MEMORY (your personal notes)` 表示 Agent 自己的长期笔记，`USER PROFILE (who the user is)` 表示用户画像，`§` 用来分隔条目。usage 则把容量也暴露给模型：这不是无限上下文，而是一块很小的长期事实区。

这一层还有个容易误读的点：memory block 进入的是 volatile tier，但这不等于每一轮都会变化。Hermes 构造 system prompt 后会缓存整段 prompt。正常 turn 里，即使 `memory` 工具刚写入了新条目，当前 session 的 system prompt 也不会立刻重新渲染；工具会返回 live state，文件也已经落盘，但模型 prompt 里看到的还是启动时的 snapshot。这样做主要是为了保住 prefix cache。例外是 context compression，压缩会触发 system prompt invalidation，随后重新从磁盘加载 memory。

`MEMORY_SCHEMA` 是第三层。它表面上是工具参数定义，但 description 也在提示模型：什么时候保存、写到 `memory` 还是 `user`、什么内容跳过。Hermes 把这些规则同时放在 system guidance 和 tool schema 里，是因为模型真正决定要不要写 memory 时，工具描述就在当前行动空间里。

所以内置文件 memory 可以概括成一句话：启动时读文件进 prompt，对话中用工具改文件，下一次 session 再把新文件读回 prompt。安全扫描放在这条路径上也就合理了；进入 `MEMORY.md` 的内容之后会变成 system prompt 的一部分，它的风险更接近 context file，而不是普通日志。

## 文件层的防护

内置文件记忆看起来很简单，但代码里有几层工程防护。

写入前会用 threat pattern 扫描内容，因为这些条目之后会进入 system prompt。命中注入或外泄类模式时，写入会被拒绝。启动时从磁盘加载已有条目也会扫描；可疑条目不会进入 prompt snapshot，但仍保留在 live state 里，方便通过工具查看和删除。

写文件时使用 lock 和 atomic replace。每次 add / replace / remove 前都会重新读取磁盘，避免多个 session 同时写入时覆盖对方。代码还会检测外部 drift：如果文件被 patch、shell append、手工编辑成不能按 `§` 分隔符稳定 round-trip 的形态，Hermes 会先生成备份并拒绝本次写入，避免把额外内容静默丢掉。

这说明内置 memory 的目标不是“大规模召回”，而是少量、高信任、可控注入。

## 外部 Provider 层

外部 memory 通过 `agent/memory_provider.py` 定义接口，再由 `agent/memory_manager.py` 统一调度。插件目录在 `plugins/memory/<name>/`，当前实现包括 Mem0、Honcho、Hindsight、Holographic、RetainDB、ByteRover、Supermemory 等。

`MemoryProvider` 的接口覆盖了一个 Agent turn 的多个位置：

| 接口 | 位置 | 作用 |
| --- | --- | --- |
| `is_available()` | Agent 初始化前 | 判断 provider 是否可启用，不做网络调用 |
| `initialize()` | Agent 初始化 | 接收 `session_id`、`hermes_home`、platform、用户和 chat 信息 |
| `system_prompt_block()` | system prompt 构造 | 注入 provider 的静态说明或状态 |
| `on_turn_start()` | 每个用户 turn 开始 | 更新 turn 计数、cadence、provider 内部状态 |
| `prefetch()` | LLM 调用前 | 返回本 turn 要注入的召回上下文 |
| `queue_prefetch()` | turn 结束后 | 为下一 turn 预热召回 |
| `sync_turn()` | turn 完成后 | 把用户输入、最终回答和可选 message trace 写入 provider |
| `on_pre_compress()` | 压缩前 | 在历史消息被丢弃前提取信息 |
| `on_session_end()` | session 边界 | 做最终抽取、flush 或清理 |
| `on_session_switch()` | session id 旋转 | 更新 provider 缓存的 session state |
| `on_memory_write()` | 内置 `memory` 工具写入后 | 把内置文件记忆同步给外部 provider |
| `get_tool_schemas()` / `handle_tool_call()` | 工具层 | 暴露 provider 自己的检索、写入或推理工具 |

`MemoryManager` 负责 fan-out、异常隔离、工具路由和 schema 去重。它允许内置 provider 加一个外部 provider，但在实际 Agent 初始化路径里，内置文件记忆不是作为 `MemoryProvider` 注册进 manager 的；它由 `_memory_store` 和 `memory` 工具单独处理。外部 provider 则只在 `memory.provider` 配置存在时创建 `MemoryManager`。

这个细节很重要：Hermes 文档里说外部 provider 与内置 memory 并行，这是真的；但代码结构上不是“两个 provider 都挂在同一个 manager 下面”。内置文件写入通过显式 bridge 调到 `MemoryManager.on_memory_write()`。

## Turn 里的数据流

一个普通用户 turn 里，memory 相关路径大致是这样：

1. Agent 初始化时读取配置。启用内置 memory 时，`MemoryStore` 加载 `MEMORY.md` / `USER.md`。配置了 `memory.provider` 时，加载一个外部 provider，调用 `initialize_all()`。
2. 构造 system prompt 时，内置文件 snapshot 进入 volatile prompt block；外部 provider 的 `system_prompt_block()` 也进入 volatile block。
3. 用户消息进入 `conversation_loop` 后，Hermes 先调用 `on_turn_start()`，再对原始用户消息调用 `prefetch_all()`。
4. `prefetch_all()` 的结果不会写回 session history，而是在 API call 前拼到当前用户消息后面。Hermes 用 `<memory-context>` 包起来，并加 system note，告诉模型这是 recalled memory context，不是新的用户输入。
5. 工具循环里，如果模型调用内置 `memory` 工具，`MemoryStore` 更新文件；如果 action 是 `add` 或 `replace`，Hermes 额外调用外部 provider 的 `on_memory_write()`。
6. 如果模型调用的是 provider 自己的工具，例如 `mem0_search` 或 `hindsight_recall`，`MemoryManager.handle_tool_call()` 按工具名路由到对应 provider。
7. turn 正常完成后，`_sync_external_memory_for_turn()` 调用 `sync_all()`，再调用 `queue_prefetch_all()`。这一步使用 original user message，不使用已经拼了技能或 memory context 的 user message。
8. 如果 turn 被用户中断，外部 memory sync 会跳过。部分回答、未完成工具链、stream 中断都不会被当作长期事实写入外部后端。

这条路径里，memory context 是 API-call-time injection，不是持久消息。session DB 里保留的是原始用户消息，不包含 `<memory-context>`。

## Context Fencing

外部 provider 返回的召回文本会被 `build_memory_context_block()` 包成：

```text
<memory-context>
[System note: ...]

provider recall text
</memory-context>
```

这里有两个目的。第一，模型能区分 recalled memory 和用户新输入。第二，Hermes 可以在输出流里识别这类 block，避免 provider context 泄漏到用户可见回复。

`sanitize_context()` 会清掉 provider 返回的预包裹 memory block，避免 provider 自己重复包一层。`StreamingContextScrubber` 则处理 streaming delta 被切碎的情况：即使 `<memory-context>` 和 `</memory-context>` 分散在多个 chunk 里，也会把内部内容从可见输出里剔除。

这不是权限系统，只是上下文隔离和展示层清理。真正的信任边界仍然在 provider、工具权限、数据发送范围和配置里。

## Session 和 Compression 边界

Hermes 的 memory 设计很重视 session id 旋转。因为外部 provider 经常缓存当前 session、document id、turn buffer 或预取结果，如果 `/new`、`/reset`、`/resume`、`/branch`、context compression 后没有通知 provider，之后的写入可能落到旧 session。

`on_session_switch()` 就是为这个问题加的。compression 路径会先触发旧 session 的 memory extraction，再结束旧 session、创建新 session，然后调用 provider 的 `on_session_switch(new_session_id, parent_session_id=old_session_id, reset=False, reason="compression")`。

Hindsight 的实现能看出这个 hook 为什么存在：它在 session switch 时会 flush 旧 buffer、清空旧 prefetch 结果、更新 `_session_id`、重新生成 `_document_id`，并清空 turn buffer。否则新 session 的内容可能继续追加到旧 document，或者第一轮召回读到旧 session 的缓存。

## Provider 例子：Mem0

Mem0 provider 展示了比较典型的外部 memory 接法：

- `system_prompt_block()` 只放 provider 状态和可用工具提示。
- `sync_turn()` 把 user / assistant turn 交给 Mem0 API 做抽取。
- `queue_prefetch()` 后台搜索相关 memory，为下一 turn 存 `_prefetch_result`。
- `prefetch()` 在下一 turn 前取出缓存并清空。
- 工具层暴露 `mem0_profile`、`mem0_search`、`mem0_conclude`。
- 读用 `user_id` 过滤，写用 `user_id + agent_id` 归因。
- 连续失败后打开 circuit breaker，短时间内停止 API 调用，避免 provider 故障拖慢主流程。

这类 provider 把“怎么抽取、怎么去重、怎么语义召回”交给外部系统。Hermes runtime 只定义什么时候给它数据、什么时候取回上下文、以及失败时不能阻塞主对话。

## 设计取舍

Hermes 的 memory 设计不是把所有历史都塞进长期记忆，而是分层处理：

- 稳定、短小、高频使用的信息进内置文件 memory。
- 大量历史对话保留在 session DB，用 `session_search` 查。
- 语义召回、用户建模、自动抽取交给外部 provider。
- 当前 turn 的召回结果只临时注入，不污染 session history。
- provider 故障按 best-effort 处理，不影响主回答返回。

这套设计的重点不是“记得越多越好”，而是控制 memory 进入模型上下文的时机、来源和成本。内置文件 memory 牺牲容量换稳定和可解释；外部 provider 牺牲一部分可控性换跨 session 召回和自动抽取。

## 源码入口

- [MemoryStore / memory tool](https://github.com/NousResearch/hermes-agent/blob/main/tools/memory_tool.py)
- [Prompt builder memory guidance](https://github.com/NousResearch/hermes-agent/blob/main/agent/prompt_builder.py)
- [System prompt assembly](https://github.com/NousResearch/hermes-agent/blob/main/agent/system_prompt.py)
- [MemoryProvider](https://github.com/NousResearch/hermes-agent/blob/main/agent/memory_provider.py)
- [MemoryManager](https://github.com/NousResearch/hermes-agent/blob/main/agent/memory_manager.py)
- [Agent init memory wiring](https://github.com/NousResearch/hermes-agent/blob/main/agent/agent_init.py)
- [Conversation loop memory injection](https://github.com/NousResearch/hermes-agent/blob/main/agent/conversation_loop.py)
- [Compression session switch](https://github.com/NousResearch/hermes-agent/blob/main/agent/conversation_compression.py)
- [Mem0 provider](https://github.com/NousResearch/hermes-agent/blob/main/plugins/memory/mem0/__init__.py)
- [Hindsight provider](https://github.com/NousResearch/hermes-agent/blob/main/plugins/memory/hindsight/__init__.py)

## 相关 Prompt Surface

Hermes 内置文件记忆相关的 prompt surface 主要有三处。源码里的 prompt 主要是英文，这里保留英文语义，再补一层中文对应说明。

### `MEMORY_GUIDANCE`

来源：[agent/prompt_builder.py](https://github.com/NousResearch/hermes-agent/blob/main/agent/prompt_builder.py)

这段 guidance 在 `memory` 工具可用时进入 stable system prompt。它定义了内置 memory 的语义边界：

英文要点：

- persistent memory across sessions
- save durable facts with the `memory` tool
- user preferences, environment details, tool quirks, stable conventions
- compact and focused on facts that will still matter later
- prioritize reducing future user steering
- user preferences and recurring corrections outrank procedural task details
- do not save task progress, session outcomes, logs, temporary TODOs, PR numbers, issue numbers, commit SHAs, phase records, file counts
- workflows belong in skills
- write memories as declarative facts, not instructions

中文对应：

- memory 是跨会话持久存在的少量事实层。
- 写入路径是 `memory` 工具，而不是模型自己“记住”。
- 适合写入的是用户偏好、环境事实、工具怪癖、项目长期约定。
- memory 在设计上保持短小，只保留之后仍会影响行为的信息。
- 价值判断是减少用户之后重复纠正、重复说明的次数。
- 用户偏好和反复纠正，比一次任务里的过程细节更重要。
- 任务进度、完成记录、PR / issue / commit 等容易过期的信息不进入 memory。
- 工作流、操作步骤和解决问题的方法进入 skill，不进入 memory。
- memory 写成事实，不写成命令。

这里最关键的是最后一点：Hermes 明确区分 fact 和 instruction。`User prefers concise responses` 是事实；`Always respond concisely` 会在之后的 session 里变成一条更强的指令，可能覆盖当前用户请求。

### Memory Snapshot Block

来源：[tools/memory_tool.py](https://github.com/NousResearch/hermes-agent/blob/main/tools/memory_tool.py)

`MEMORY.md` 和 `USER.md` 进入 system prompt 时不是裸文件内容，而是被渲染成带 header、usage 和分隔符的 block：

英文形态：

```text
══════════════════════════════════════════════
MEMORY (your personal notes) [<usage>% - <current>/<limit> chars]
══════════════════════════════════════════════
<entry>
§
<entry>
```

`USER.md` 对应的 header 是：

```text
USER PROFILE (who the user is) [<usage>% - <current>/<limit> chars]
```

中文对应：

- `MEMORY (your personal notes)`：这是 Agent 自己的长期笔记。
- `USER PROFILE (who the user is)`：这是关于用户的长期画像。
- `[<usage>% - <current>/<limit> chars]`：这是容量提示，让模型知道 memory 不是无限上下文。
- `§`：这是条目分隔符，让每条 memory 的边界更清楚。

这个 block 本身也是 prompt 设计的一部分。header 告诉模型这段内容是什么，usage 告诉模型容量边界，`§` 分隔符让条目边界清楚，也让 `replace` / `remove` 的 substring matching 更稳定。

### `MEMORY_SCHEMA`

来源：[tools/memory_tool.py](https://github.com/NousResearch/hermes-agent/blob/main/tools/memory_tool.py)

`memory` 工具 schema 也承担 prompt 作用。它不是只有参数定义，还在 description 里说明什么时候写、写到哪里、什么不要写。

英文工具表面大致是：

```text
name: memory
action: add | replace | remove
target: memory | user
content: entry content
old_text: substring used by replace/remove
```

其中 `target` 的语义是：

- `user`：用户是谁、偏好、沟通方式、长期约束。
- `memory`：Agent 对环境、项目、工具和经验的笔记。

中文对应：

- `add`：新增一条长期事实。
- `replace`：用 `old_text` 找到旧条目并替换。
- `remove`：用 `old_text` 找到旧条目并删除。
- `target=user`：写入用户画像。
- `target=memory`：写入 Agent 对环境、项目、工具、经验的笔记。
- `content`：新条目内容。
- `old_text`：替换或删除时用于定位旧条目的短文本。

schema description 和 `MEMORY_GUIDANCE` 有意重复了一些规则：保存 durable information，跳过 trivial info、raw data dumps 和 temporary task state。这样模型在决定是否调用工具时，能在工具说明里再次看到 memory 的边界。

### `<memory-context>`

来源：[agent/memory_manager.py](https://github.com/NousResearch/hermes-agent/blob/main/agent/memory_manager.py)

这块主要用于外部 provider 的 prefetch context，不是内置文件记忆，但它属于同一个 memory 注入问题。

外部 provider 返回的召回内容会被包成：

英文形态：

```text
<memory-context>
[System note: recalled memory context, not new user input]

<provider context>
</memory-context>
```

中文对应：

- `<memory-context>`：这里开始是一段召回来的 memory context。
- `not new user input`：这不是用户刚刚输入的新请求。
- `<provider context>`：这里放外部 provider 返回的召回结果。
- `</memory-context>`：召回上下文结束。

它的作用是把 recalled memory 和用户当轮输入隔开。模型可以使用这段内容，但这段内容不是用户刚刚说的话。Hermes 还会在 streaming 输出层 scrub 这个 block，避免 provider context 被原样泄漏到用户可见回复里。
