# Stage 3 Review Report

Cross-file consistency review after the 32-commit doc rewrite. The 4 most-rewritten files (`Hermes Agent Memory.md`, `LLM RL Infra.md`, `Skills.md`, `Agent Loop.md`) and 27 of the 28 polish files were all touched. Findings below.

## Summary

- Files reviewed: 59 concept pages (35 in scope of audit, plus 24 in `02-ai-directions/概念/` and `04-ai-systems/rag/概念/` index pages)
- Files modified in rewrite: 31 unique files across 32 commits
- Blocking issues: 0
- Non-blocking observations: 3

## Blocking issues (must fix)

None. All hard forbidden patterns (`## 面试怎么讲` / `## 白板问题` / `Python 单例` / `singleton` in concept pages) are absent. Cross-file coherence holds (GRPO ↔ LLM RL Infra deferral works in both directions; Memory.md is a clean index pointing back to Hermes / OpenClaw / Mem0 / LangGraph; Agent 概念.md is unchanged as the agent-direction map).

## Non-blocking observations

These are not blockers. Each is a soft call: a future polish pass could remove them, but they are not in the banned-pattern list and reading them in context, they read as deliberate voice choices, not regressions.

- `02-ai-directions/ai-infra/概念/Observability.md:5` — second-person in opening paragraph: "你需要把它拆成可观察的事件和指标。" Borderline because it is a "you need to" framing in a section opener. Acceptable, since the rest of the page is third-person and this is one of two times the pattern appears. Could be softened to "拆分路径..." in a future polish.
- `03-engineering-basics/概念/WebSocket.md:38` and `:51` — two second-person uses ("你需要自己处理...", "你需要自己定义..."). Both describe what a developer implementing WebSocket must handle; defensible as walkthrough context (the WebSocket protocol genuinely requires the developer to define these things). Acceptable, but inconsistent with the third-person default.
- `02-ai-directions/agent/概念/Skills.md:9, 26, 38, 42, 46` — five uses of "必须" / "必须放". These are technical statements of where rules belong ("`不要泄露 secrets` 必须放 `AGENTS.md`"), not moralizing imperatives directed at the reader. The style guide's banned pattern was "必须遵守" / "一定要" / "应该注意" / "需要特别小心" — these don't match. Keeping them is fine.

## Wikilink check

- Total unique wikilinks across all concept pages: 238
- Broken: 0
- Resolution: every wikilink resolves to an existing file via the resolver (same-dir `.md`, basename, or relative path including cross-folder links like `[[RAG]]` → `04-ai-systems/rag/RAG.md`).
- Section-anchor wikilinks (e.g., `[[LLM RL Infra#GRPO 公式速览]]`, `[[SSE#和 WebSocket 对照]]`) resolve to the file; the section anchors themselves were not validated against heading text, but this is the same risk level as a typo in any wikilink and is not specific to the rewrite.

## Forbidden pattern scan

- `## 面试怎么讲` / `## 面试` / `## 面试问题` / `## 白板问题`: 0 hits across 59 concept pages.
- `Python 单例` / `singleton` in concept pages: 0 hits. (LLM RL Infra.md no longer contains the banned section.)
- Second-person address (`你应该` / `你需要` / `你必须` / `你不能`): 3 hits in 2 files (WebSocket.md ×2, Observability.md ×1). Each is in walkthrough-like context describing what a developer must handle, not pure moralizing. See "Non-blocking observations" above.
- Generic 5-7-bullet `## 常见失败模式` / `## 边界和失败模式` / `## 常见错误` ending: 14 concept pages still have these sections. Read each one: bullets are concept-specific in all 14 (e.g., Attention.md talks about attention-weight-as-explanation and retrieval dilution; HTTP.md talks about retry-on-POST-side-effects; Security.md talks about RAG tenant filtering). None of the remaining 失败模式 sections are generic-software-engineering filler. The bar from the style guide ("would this bullet be a useful warning to someone who has already read the rest of the page?") is met in all 14.

## Voice spot-check

**5 most-rewritten files:**

- `02-ai-directions/agent/概念/Hermes Agent Memory.md` — PASS. Opens by distinguishing the two parallel memory paths (built-in files vs external provider), not with a generic "memory is..." definition. Third-person throughout. Closing "设计的取舍" section is honest about the design tradeoff, not moralizing. The full prompt blocks (`MEMORY_GUIDANCE`, `MEMORY_SCHEMA`, `<memory-context>`) are no longer reproduced with line-by-line gloss — design rationale is what's on the page.
- `02-ai-directions/rl/概念/LLM RL Infra.md` — PASS. Opens with the real engineering problem ("把算法公式、rollout 数据、训练框架... 连接成一条稳定的系统链路"), not with "什么是 LLM RL Infra". No `## Python 单例`, no `## 白板问题`. The "GRPO 是不是 on-policy" section handles the off-policy / near-on-policy nuance honestly, including a self-critical note that real training systems easily slide off-policy due to rollout lag. Math is present where it earns its place.
- `02-ai-directions/agent/概念/Skills.md` — PASS. Opens with "Skill 本质上还是 prompt" and the actual engineering purpose ("把'我每次都要重复告诉 Agent 的做事方法'沉淀成可版本管理的小模块"). The AGENTS.md / Skill / Tool comparison is one paragraph in `## Skill、AGENTS.md、Tool 的关系`, not a children's-book climax. The progressive-disclosure section is the load-bearing part of the page. No "面试怎么讲" section, no 8-bullet generic 常见错误 ending.
- `02-ai-directions/agent/概念/Agent Loop.md` — PASS. Opens by distinguishing "LLM from一次性生成变成可执行系统", then goes to "和 workflow 的边界" (linking to Anthropic's workflow/agentic-system distinction) before getting into Codex specifics. The "## 失败模式" bullets are all Agent-Loop-specific (budget/stop conditions, structured tool errors, state-in-context, trace granularity, premature multi-agent). No `### 把 loop 写成无限自治` moralizing subheading. Source-line citations are condensed — Codex `run_turn` is the load-bearing link, with secondary references in narrative form.
- `02-ai-directions/agent/概念/Memory.md` — PASS. The audit's specific note ("Not really a concept page — it's an index of subpages") is addressed. The page opens with "这页是 memory 的入口索引，不做统一概论" and points to [[Agent 概念]] for the broader map. Each system (Hermes / OpenClaw / Mem0 / LangGraph) is a section with its own links and a "相关问题集中在..." summary. The "容易混在一起的东西" closing list is concept-specific.

**3 polish files (ending sections):**

- `02-ai-directions/rl/概念/Reward.md` — PASS. The opening "奖励写得不对，模型会认真学错" is exactly the right line. No 失败模式 section to check; the page ends at `## 参考资料`. Sparse-vs-dense tradeoff is present.
- `02-ai-directions/rl/概念/PPO.md` — PASS. Opening "更新 policy 时不能一步迈太大" lands the engineering reason for PPO. No 失败模式 ending; closes on references. The page is short but each sentence earns its place.
- `02-ai-directions/rl/概念/Policy.md` — PASS. "随机策略对探索很重要" is present (the audit called this out as a good line to keep). The generic 失败模式 ending has been dropped. Page closes on `## 参考资料`.

## Cross-reference coherence

- `GRPO.md` → `[[LLM RL Infra]]` (with section anchors `[[LLM RL Infra#GRPO 公式速览]]` and `[[LLM RL Infra#GRPO batch 应该保存什么]]`): both anchors resolve to a file that does have these sections. Confirmed.
- `LLM RL Infra.md` → `[[GRPO]]` for full derivation: confirmed (line 5: "算法层面的 GRPO 推导放在 [[GRPO]]，本篇不重复"). Bidirectional deferral works.
- `Memory.md` → [[Hermes Agent Memory]], [[Agent 概念]], with the four subpages (Hermes / OpenClaw / Mem0 / LangGraph) being the actual content. Hermes Agent Memory.md links back to `[[Memory]]` for the index entry. Confirmed.
- `Skills.md` links to `[[Prompting]]`, `[[Context Engineering]]`, `[[Tool Use]]`, `[[Prompt Cache]]`, `[[Security]]`, `[[Evals]]` in the "相关概念" section. All resolve.
- `Agent Loop.md` links to `[[Agent]]`, `[[Tool Use]]`, `[[ReAct]]`, `[[Context Engineering]]`, `[[Evals]]`, `[[Security]]`, `[[Cost]]`, `[[Streaming Response]]` in its "相关概念" section. All resolve.

No broken cross-references identified.

## Commit hygiene

- Total commits since audit base `2622b11`: 32
- Files modified: 31 unique (Skills.md was touched twice: "Rewrite Skills..." + "Add brief verification + security note to Skills"). Atomic one-file-per-commit holds.
- Uncommitted changes: 0 markdown files. The only untracked items are `.DS_Store`, `02-ai-directions/agent/.DS_Store`, and `.lumina/` — repo-level noise, not doc content.
- Vague commit messages: 0. Every commit has a specific subject naming the file and the change ("Polish X: drop generic 5-bullet failure-mode ending", "Condense Agent Loop source-line citations", "Reframe Memory.md as explicit index page", etc.). No "misc" / "updates" / "fix" placeholders.

## Final verdict

The 32-commit rewrite hit its goals. All hard forbidden patterns are gone. Wikilink graph is intact. The 4 rewrite files (Hermes Agent Memory, LLM RL Infra, Skills, Agent Loop) all have the OpenAI-engineering-blog voice in their opening and key sections. The 14 retained 失败模式 sections are concept-specific, not generic. Commit hygiene is clean (one file per commit, descriptive messages, no uncommitted markdown). No blocking issues. Three minor second-person uses remain in WebSocket and Observability that could be softened in a future polish but are not regressions.
