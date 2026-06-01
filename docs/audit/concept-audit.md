# Concept Page Audit

Per-file audit of the 35 concept pages. Severity is one of:

- **rewrite** — heavy AI flavor / code-field obsession / wrong structure. Full rewrite, not a polish.
- **polish** — mostly fine, but has 1-3 specific patterns that need to go (usually a 5-7 bullet 失败模式 ending, or a trivial 最小例子, or a moralizing line).
- **leave** — already in target voice. Either do nothing, or only fix typos / broken wikilinks.

4 files are `rewrite`. The rest of the issues are concentrated in the "## 常见失败模式" / "## 边界和失败模式" 5-7 bullet pattern that almost every page has. Severity is the writer's call; this audit flags the page-level risk.

## Summary

- `rewrite`: 4 files
- `polish`: 17 files
- `leave`: 14 files

## Per-file table

| File | Severity | Top issues | What to keep |
| --- | --- | --- | --- |
| `01-foundations/概念/Attention.md` | polish | Ends with 4 generic "常见工程失败" bullets; the "## 边界和失败模式" close is a formula | Math is well-placed; 工程形态 is concrete (KV cache, FlashAttention); the "head ≠ grammar head" hedge is good |
| `01-foundations/概念/Context Window.md` | polish | "## 边界和失败模式" 4-bullet ending is generic ("把 context window 当长期记忆") | The token-budget table is concrete and useful; opens with what counts in the window |
| `01-foundations/概念/Embedding.md` | polish | "## 边界和失败模式" 5-bullet ending is mostly generic | The "two uses" distinction (model-internal vs retrieval) is the right opener; vector-store search example is real |
| `01-foundations/概念/Inference.md` | polish | "## 边界和失败模式" 5-bullet ending is generic | prefill/decode split is clean; the "应用层通常需要把模型调用包成..." framing is right |
| `01-foundations/概念/Token.md` | polish | "## 边界和失败模式" 4-bullet ending is generic; the closing aphorism "Token 不是自然语言单位" is fine but close to moralizing | The "别用字符数估算成本" warning is specific and good |
| `01-foundations/概念/Transformer.md` | leave | — | Self-critical "Agent 误读" note; good opener; math is light; 工程形态 is concrete |
| `02-ai-directions/agent/概念/Agent Loop.md` | **rewrite** | Heavy Codex source-line citations (a dozen `codex-rs/.../foo.rs#L119-L133` in one section); section names like "### 把 loop 写成无限自治" moralize at the reader; 175 lines with massive code-citation density | The Codex `run_turn` walkthrough structure (problem → mechanism → engineering shape → Codex specifics → stop conditions) is good; keep that ordering |
| `02-ai-directions/agent/概念/Agent 概念.md` | leave | — | A clean map of agent-related concepts; no failure-mode dump; "不要一开始就把这些都塞进去" is honest |
| `02-ai-directions/agent/概念/Memory.md` | polish | Not really a concept page — it's an index of subpages (Hermes, OpenClaw, Mem0, LangGraph). Either keep as explicit index or merge into Agent 概念 | The "容易混在一起的东西" list of 5 things-that-might-be-called-memory is good |
| `02-ai-directions/agent/概念/Hermes Agent Memory.md` | **rewrite** | Reproduces 3 full prompt blocks (`MEMORY_GUIDANCE`, Memory Snapshot Block, `MEMORY_SCHEMA`) verbatim with line-by-line Chinese gloss; `<memory-context>` block pasted in English+Chinese; 12-row `MemoryProvider` interface table; 310 lines; half the doc is paraphrasing the source | The frozen-snapshot / internal-vs-external split / session-rotation design points are good and should stay; the block-quoting must go |
| `02-ai-directions/agent/概念/ReAct.md` | leave | — | This is the target voice. Do not touch. |
| `02-ai-directions/agent/概念/Single Agent vs Multi-Agent.md` | leave | — | "默认先做 Single Agent" opener is direct; "没有这些收益，多 Agent 只是复杂度" is honest |
| `02-ai-directions/agent/概念/Skills.md` | **rewrite** | Has "## 面试怎么讲" section with a 6-paragraph sample answer; AGENTS.md vs Skill vs Tool comparison climax is a children's-book checklist ("AGENTS.md = 常驻规则 / Skill = 按需加载 / Tool = 可调用动作"); ends with 8 generic "常见错误" bullets; an entire prose answer to "如果面试官问：..." | The progressive-disclosure explanation (lazy load → 代价 → 例子) is the strongest part and should stay |
| `02-ai-directions/agent/概念/Tool Use.md` | polish | "## 边界和失败模式" 5-bullet ending is mostly generic | "工具调用是 Agent 从说到做的分界线。这里不能靠运气" is on-the-nose but defensible; the JSON tool-call example is concrete |
| `02-ai-directions/ai-infra/概念/GPU Basics.md` | polish | "## 常见失败模式" 5-bullet ending is somewhat generic ("量化后只看能否加载，不看质量") | The "很多人看到 GPU 利用率不高就以为还有空间" opener is direct and good |
| `02-ai-directions/ai-infra/概念/Inference Serving.md` | polish | "## 常见失败模式" 5-bullet ending is generic; the vLLM/SGLang/llama.cpp callout could be sharper | "兼容接口不等于兼容行为" is the right kind of hedge |
| `02-ai-directions/ai-infra/概念/Model Gateway.md` | polish | "## 常见失败模式" 5-bullet ending is generic | The MVP list is concrete and useful; "不要把所有供应商参数暴露给业务方" is a real point |
| `02-ai-directions/ai-infra/概念/Model Routing.md` | polish | "## 常见失败模式" 5-bullet ending is generic | "路由不是简单的失败就换模型" is the right opener |
| `02-ai-directions/ai-infra/概念/Observability.md` | leave | — | The "三类信号" framing is clean; the "AI 系统至少要记录" list is specific to AI systems, not generic SRE |
| `02-ai-directions/post-train/概念/DPO.md` | polish | "## 常见失败模式" 5-bullet ending is generic; the page is short but fine | "DPO 不是万能对齐按钮" is honest |
| `02-ai-directions/post-train/概念/GRPO.md` | polish | "## 常见失败模式" 5-bullet ending is generic; "## 核心公式" duplicates material that lives in LLM RL Infra | The on-policy/off-policy nuance is well-handled |
| `02-ai-directions/post-train/概念/Preference Optimization.md` | polish | "## 常见失败模式" 5-bullet ending is mostly generic | The "如果标注规则不清，模型会学到混乱偏好" hedge is good |
| `02-ai-directions/post-train/概念/RLHF.md` | polish | "## 常见失败模式" 5-bullet ending is generic; light on engineering shape compared to LLM RL Infra | The "经典 pipeline" diagram is clean |
| `02-ai-directions/post-train/概念/Reward Model.md` | polish | "## 常见失败模式" 5-bullet ending is somewhat generic; the opener "它不是客观真理" is a good line | The "policy 学会刷 reward" point lands |
| `02-ai-directions/post-train/概念/SFT.md` | polish | "## 常见失败模式" 5-bullet ending is generic | "先准备 100 到 1000 条高质量任务样例" is concrete |
| `02-ai-directions/pre-train/概念/Checkpoint.md` | polish | "## 常见失败模式" 5-bullet ending is generic; opener is dry but acceptable | The "只保存权重可以用于推理，但不一定能无缝恢复训练" hedge is the right opener |
| `02-ai-directions/pre-train/概念/Dataset Pipeline.md` | polish | "## 常见失败模式" 5-bullet ending is generic | The "data/raw/, data/processed/, tokenizer.json" minimum is useful |
| `02-ai-directions/pre-train/概念/Distributed Training.md` | polish | "## 常见失败模式" 5-bullet ending is somewhat generic; "并行不是免费加速" is a good line | The "常见并行" list is the right level for a concept page |
| `02-ai-directions/pre-train/概念/Tokenizer Training.md` | leave | — | "tokenizer 是训练产物，不是随便选的字符串函数" is honest; the 最小实验 is a real experiment, not filler |
| `02-ai-directions/pre-train/概念/Training Loop.md` | leave | — | The "理解它，比先背训练平台名更重要" opener is direct; the bare 6-line loop is appropriate |
| `02-ai-directions/rl/概念/Environment.md` | polish | "## 常见失败模式" 5-bullet ending is generic | "没有清楚的环境，就没有可训练的 RL 问题" is the right opener |
| `02-ai-directions/rl/概念/LLM RL Infra.md` | **rewrite** | Has "## Python 单例" section (Python singleton has nothing to do with LLM RL Infra); "## 白板问题" section is a 12-bullet interview checklist; opens with "面试真正考的是"; "面试回答要避免两个错误：错误 1 / 错误 2"; GRPO/buffer/DAPO/GSPO/entropy-collapse material is interview-framed throughout | The math, the buffer field list, the DAPO/GSPO/entropy-collapse analysis are all good and should stay; the interview framing and the off-topic sections should go |
| `02-ai-directions/rl/概念/Offline RL.md` | polish | "## 常见失败模式" 5-bullet ending is generic | "数据里没覆盖的动作，模型不该自信地乱试" is the right kind of honest line |
| `02-ai-directions/rl/概念/PPO.md` | polish | "## 常见失败模式" 5-bullet ending is somewhat generic; the page is short | "它解决的核心工程问题是：更新 policy 时不能一步迈太大" is a good opener |
| `02-ai-directions/rl/概念/Policy.md` | polish | "## 常见失败模式" 4-bullet ending is generic | "随机策略对探索很重要" is concise and correct |
| `02-ai-directions/rl/概念/Reward.md` | polish | "## 常见失败模式" 5-bullet ending is generic; "奖励写得不对，模型会认真学错" is a good line | The sparse-vs-dense tradeoff is worth keeping |
| `03-engineering-basics/概念/API Auth.md` | leave | — | "prompt 不是安全边界" is the right opener; the `run_tool` policy check is concrete |
| `03-engineering-basics/概念/HTTP.md` | leave | — | "这里不把 HTTP 写成面试题" is exactly the right framing |
| `03-engineering-basics/概念/SSE.md` | polish | Long (auto-reconnect, browser compat, SSE-vs-WS table); the table is useful but heavy; otherwise clean | The "反向代理缓冲" point is the right opener for AI app readers |
| `03-engineering-basics/概念/Streaming Response.md` | leave | — | The "流式响应的完成标准不是能看到字一个个出来" is the right framing |
| `03-engineering-basics/概念/WebSocket.md` | polish | Mirrors SSE.md structure (auto-reconnect, browser compat, SSE-vs-WS table); the table is fine; some sections are denser than they need to be | "AI 应用里不要一看到实时就上 WebSocket" is direct and right |
| `04-ai-systems/概念/Context Engineering.md` | leave | — | The "它比 Prompting 宽" framing is good; the context budget composition is concrete |
| `04-ai-systems/概念/Cost.md` | leave | — | "很多 demo 看起来能跑，一到真实用户就撑不住" is the right opener |
| `04-ai-systems/概念/Evals.md` | leave | — | The RAG-vs-Agent eval split is concrete; the "happy path" critique is honest |
| `04-ai-systems/概念/LLM.md` | leave | — | "LLM 不是数据库查询，也不是规则引擎" is the right opener |
| `04-ai-systems/概念/Prompt Cache.md` | leave | — | "它优化的是重复计算，不保证答案质量变好" is the right opener |
| `04-ai-systems/概念/Prompting.md` | leave | — | "它和 Context Engineering 的关系是..." framing is right |
| `04-ai-systems/概念/Security.md` | leave | — | "prompt 不是安全边界" is the right opener; the `execute_tool` example is concrete |

## Notes for writers

- The "## 边界和失败模式" / "## 常见失败模式" / "## 常见错误" 5-7-bullet ending is the most formulaic pattern in the current repo. Almost every page has it. A writer rewriting a `polish` page should evaluate each bullet: if it is specific to this concept and tells the reader something they cannot infer from elsewhere on the page, keep it; if it is generic software-engineering wisdom ("缺少监控"、"忽略安全"、"没有测试"), cut it. The bar is: would this bullet be a useful warning to someone who has already read the rest of the page? If not, cut.

- The "## 最小例子" / "## 最小观察" / "## 最小实验" sections are fine when the example is real (the 6-line training loop in Training Loop.md is real; the 5-line singleton in LLM RL Infra.md is filler). Cut fillers, keep real examples.

- Source-code citations (`[file.py#L10-L30](url)`) are part of the voice when they anchor a claim. They are noise when they appear 5+ times in one section (see Agent Loop.md). Cite the load-bearing claims, not every interesting line.
