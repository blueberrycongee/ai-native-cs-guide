# Agent 信息来源

这页记录 Agent 方向的信息源。

链接数量不是目标。这里先放信息密度高、能验证、能帮助做技术判断的来源。

## 一级来源

一级来源通常是官方文档、代码仓库、release note、技术博客或论文。先看这些，少被二手解读带跑。

- [OpenAI Codex GitHub](https://github.com/openai/codex)：跟进 coding agent 在终端、本地仓库、测试运行、权限控制上的产品形态。
- [OpenAI Codex Docs](https://developers.openai.com/codex)：查 Codex 的官方使用方式和能力边界。
- [opencode GitHub](https://github.com/anomalyco/opencode)：观察开源 coding agent 如何做终端交互、模型适配、文件修改和开发者体验。
- [opencode Docs](https://opencode.ai/docs)：查 opencode 的安装、配置、权限、MCP 和开发工作流。
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)：观察个人常驻 Agent、通道接入、skills、工作区和本地控制面。
- [OpenClaw Docs](https://docs.openclaw.ai/)：查 OpenClaw 的安装、channels、skills、安全和运行模型。
- [Hermes Agent GitHub](https://github.com/NousResearch/hermes-agent)：观察 skills、memory、messaging gateway、自托管 Agent 和持续学习方向。
- [Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs)：查 Hermes 的 CLI、tools、skills、memory、MCP 和架构文档。

## 论文和经典材料

这些材料不用一开始细抠公式。先弄清楚它们提出了什么问题。

- [ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629)：理解为什么 Agent 会把 reasoning 和 acting 交替组织起来。
- [Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks](https://arxiv.org/abs/2005.11401)：理解 RAG 为什么会成为 Agent 获取外部知识的基础能力之一。
- [OpenAI Prompt Caching](https://platform.openai.com/docs/guides/prompt-caching)：理解长上下文和重复前缀如何影响延迟与成本。
- [Anthropic Prompt Caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)：理解显式 cache breakpoint 这类设计。

## 二级来源

二级来源用来发现趋势，但不要直接当结论。

- GitHub Trending：看哪些 Agent 项目突然增长，但要警惕 star 泡沫。
- GitHub Issues / Pull Requests：比 README 更能看出项目真实问题。
- Hacker News：看开发者对新工具的实际反馈。
- arXiv：跟论文，但要区分 research demo 和可落地系统。
- X / Twitter：发现新项目，但信息噪音很大。
- Reddit / Discord / 论坛：看踩坑和真实使用体验，但结论需要交叉验证。

## 怎么判断一个信息源要不要看

先看这些内容：

- 是否有真实代码或可运行 demo
- 是否讲清楚限制和失败场景
- 是否解释了架构和设计取舍
- 是否有测试、评测或 benchmark
- 是否有活跃 issue、release 和社区讨论

少看这些内容：

- 只讲口号、不讲边界的营销文章
- 只有截图没有代码的 demo
- 没有边界条件的成功案例
- 把复杂 Agent 问题讲成万能 prompt 的内容
