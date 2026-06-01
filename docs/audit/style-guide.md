# Concept Page Style Guide

Target voice: OpenAI engineering blog. Direct problem statement, honest tradeoffs, math where it earns its place, no moralizing, no interview-prep Q&A.

The reference is `02-ai-directions/agent/概念/ReAct.md`. It opens with the problem the paper solves, walks the paper's actual contribution, has math where it earns its place, lists the paper's own failure modes honestly, and closes on what modern systems inherit. Every other page should aspire to that.

## Voice & tone

1. **Open with the specific problem the concept solves, not a generic definition.** "Attention 解决的问题很具体：模型处理某个 token 时，应该从上下文里的哪些位置取信息。" Not "Attention 是一种机制，让模型可以关注重要的部分。"

2. **State tradeoffs honestly, including ones that hurt the author's preferred design.** ReAct.md says ReAct 受 action/observation 结构约束, reasoning 灵活性可能下降. It does not pretend ReAct wins on every axis.

3. **Avoid second-person address.** Don't write "你应该先..." or "你需要关注...". Write "X is used when Y" or "P99 延迟主要受 prefill 和排队影响". Second-person ("你") is a tutorial smell; third-person framing is engineering-blog framing.

4. **No moralizing.** "必须遵守" / "一定要" / "应该注意" / "需要特别小心" are all red flags. The reader is a peer. If a rule is important, the surrounding context should make it obvious without imperative mood.

5. **No interview-prep Q&A.** Sections named "## 面试怎么讲" / "## 面试问题" / "## 白板问题" do not belong in concept pages. If a concept is interview-worthy, the page already explains it. The Q&A format ("如果面试官问：... 可以这样答：...") is tutorial content, not engineering content.

6. **No checklist of failure modes at the end of every doc.** "## 常见失败模式" / "## 边界和失败模式" / "## 常见错误" sections that list 5-7 generic bullets ("忽略安全"、"缺少监控"、"没有测试") are the most formulaic pattern in the current repo. The bar to include such a section should be high. If you do include it, each bullet must be specific to *this* concept, not generic software-engineering wisdom that could appear under any heading.

7. **No oversimplified analogies as authoritative mappings.** "AGENTS.md = 常驻规则 / Skill = 按需加载" is a children's-book framing. It is fine as one illustration in passing; it is not fine as the climax of a page. If the analogy oversimplifies, write the actual structure.

8. **Hedge or commit, don't oscillate.** "推荐 X，但不一定要 Y" wastes words. Either say "X is the common case; Y is for when ..." or commit to a position with reasoning.

## Structure

The previous template was "## 工作机制 / ## 工程形态 / ## 最小例子 / ## 边界和失败模式". This is the source of the formulaic feel. **It is replaced.** Use whichever sections fit the concept. Common shapes:

- **Papers / algorithms**: `## 核心机制` (the math/algorithm), `## 工程形态` (where it shows up in real systems), `## 边界` (honest about what it does not do).
- **System components**: `## 工作机制` (what it does at runtime), `## 工程形态` (its place in a real system), `## 失败模式` (only if there are specific, named failure modes, not generic ones).
- **Concepts that are mostly "where in the stack"**: skip the 最小例子 entirely. Examples should appear where they actually explain a point, not because every doc needs a "small example" section.

The closing "## 参考资料" section stays. Wikilinks stay. Inline citations to source code (file paths, PRs, paper sections) stay. Math stays.

## Code & math

- **Include code only when it explains a mechanism.** A 4-line `for batch in dataloader` loop is fine in Training Loop. A 5-line `python class Singleton` block is not fine in an RL Infra page — that is filler.
- **Do not list every field of every struct.** The `MemoryProvider` interface has 12 methods; do not list them as a 12-row table. Mention the categories, give one or two concrete examples, and link to the source.
- **Do not reproduce full prompt blocks.** `MEMORY_GUIDANCE` / `MEMORY_SCHEMA` / `<memory-context>` blocks are not the document's content; the design rationale behind them is. Quote one or two lines if needed; do not paste the whole block with Chinese gloss underneath each English line.
- **Math:** include when it earns its place. The ReAct paper's `A_hat = A ∪ L` is one line and it changes how you read the paper. The PPO/GRPO objective is denser but unavoidable if you are writing about RL. The Reward function as a `r = sum(gamma^t * reward_t)` is fine. Don't add math that doesn't sharpen the reader's mental model.

## Forbidden patterns

These are banned. Not "use sparingly". Banned.

- `## 面试怎么讲` / `## 面试` / `## 面试问题` / `## 白板问题` sections.
- `## 常见失败模式` / `## 边界和失败模式` / `## 常见错误` sections that are a list of 5-7 generic bullets applicable to any software-engineering topic.
- `## 最小例子` sections whose example is trivial padding (a 3-line `Singleton` class, a `def add(a, b)`).
- Moralizing imperatives: "必须..." / "一定要..." / "应该注意..." / "需要特别小心...".
- Oversimplified mappings presented as the page's main insight: "AGENTS.md = 常驻规则 / Skill = 按需加载 / Tool = 可调用动作".
- "一个最小 X 通常包括：- A\n- B\n- C" templates that close every doc.
- "如果面试官问：... 可以这样答：..." Q&A patterns.
- Second-person ("你应该..." / "你可以...") outside of walkthroughs where the second-person is genuinely the user acting.
- Reproducing source-code files as a 30-line block with a per-line Chinese gloss. Source code belongs in wikilinks or in well-chosen 5-10 line excerpts.

## What to keep

These are part of the voice. Do not strip them while rewriting.

- `[[X]]` wikilink syntax.
- Directory structure as-is.
- Math via `$...$` and `$$...$$`.
- Curated `## 参考资料` section at the end with real links to papers / docs / source.
- Inline citations to source code (`[file.py#L10-L30](url)`) when a claim is anchored in code.
- Code blocks when they explain a mechanism.
- `## 相关概念` lists (different from "常见错误" lists — these are wikilinks to other pages, not generic bullets).

## Good vs bad: a feel comparison

These are paraphrased from the repo. The good passage is the ReAct voice; the bad passage is the LLM RL Infra voice.

Good (ReAct.md):

> ReAct 来自论文 [ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629)。这篇论文要解决的问题不是提出一个完整的 Agent 工程架构，而是把大语言模型的两类能力放到同一个任务轨迹里：一类是语言推理，也就是 reasoning trace；另一类是面向环境的 task-specific action。论文的核心观点是：只让模型推理，容易停留在内部知识里，出现幻觉和错误传播；只让模型行动，又缺少对目标、状态和异常的显式跟踪。

Bad (LLM RL Infra.md voice):

> LLM RL Infra 不是单纯会跑 GRPO 命令。面试真正考的是：你能不能把算法公式、rollout 数据、训练框架、推理后端、MoE/并行优化和 eval 连接成一条可解释的系统链路。... 面试回答要避免两个错误：错误 1：看到 old policy 就说 GRPO 是 off-policy... 错误 2：说 GRPO 永远 on-policy。

The good passage opens with the paper's actual contribution. The bad passage opens with "what the interview tests" and then organizes the rest of the doc around answering interview questions. The good passage has the author's actual position ("ReAct 受结构约束，reasoning 灵活性可能下降"). The bad passage lists "错误 1 / 错误 2" as if there are exactly two mistakes a candidate can make.

## Specific page notes

A few pages have issues that don't fit a general rule:

- **`Hermes Agent Memory`** (310 lines) is the worst offender in the repo. It reproduces three full prompt blocks (MEMORY_GUIDANCE, Memory Snapshot Block, MEMORY_SCHEMA) verbatim with line-by-line Chinese gloss. The MemoryProvider table has 12 rows. The `<memory-context>` block is pasted with English + Chinese. Half the page is the source's content; the other half is paraphrasing the source. **A rewrite should keep the design points (frozen snapshot, MEMORY.md vs USER.md, internal-vs-external split, session rotation) and drop the block-quoting.**

- **`LLM RL Infra`** has a "## Python 单例" section. Python singleton has nothing to do with LLM RL Infra. It also has a "## 白板问题" section that is a 12-bullet interview checklist. Both sections should be deleted. The GRPO/buffer/DAPO/GSPO/entropy-collapse material is good; it just shouldn't be framed as interview prep.

- **`Skills`** has a "## 面试怎么讲" section with a 6-paragraph sample answer, plus an "## 常见错误" section with 8 generic bullets. Both should be cut. The progressive-disclosure explanation is good. The AGENTS.md / Skill / Tool comparison should be one paragraph, not the climax.

- **`Agent Loop`** has heavy Codex source-line citations (a dozen `codex-rs/.../foo.rs#L119-L133` links in one section). Some citations are useful; the page is currently citation-dense. Section names like "### 把 loop 写成无限自治" moralize at the reader. The 5 失败模式 at the end are mostly fine (they are specific to Agent Loop, not generic), so that section can stay with light editing.

- **`Memory.md`** is not really a concept page — it is an index page that links to four subpages (Hermes, OpenClaw, Mem0, LangGraph). Either keep it explicitly as an index, or merge it into Agent 概念.
