# Skills

Skill 本质上还是 prompt，但它是文件化、结构化、可复用、按需加载的 prompt。核心文件叫 `SKILL.md`，写的内容仍然是自然语言指令，只是它有固定的元数据、触发方式、目录结构，还可以带脚本和参考资料。

这里不把 Skills 讲成 Agent 的一种神秘能力。它的工程目的是把“我每次都要重复告诉 Agent 的做事方法”沉淀成可版本管理、可触发、可测试的小模块。能否达成这个目的，取决于 description 写得准不准、规则放在 skill 还是 AGENTS.md 选得对不对。

## 为什么不直接写进 AGENTS.md

`AGENTS.md` 适合放每次任务都应该看到的规则：项目怎么启动、测试怎么跑、代码风格、安全边界、哪些动作必须先问用户、哪些 skill 在什么条件下必须被调用。

Skill 适合放不是每次都需要，但一旦需要就该完整执行的流程：发布前检查、文档同步、代码审查、数据清洗、生成周报、处理某类文件、维护某个特定仓库。

区别不是风格，是加载时机。`AGENTS.md` 在任务开始前全量进入上下文；skill 先暴露 `name` 和 `description`，完整内容只有被选中才加载。这里有真实 tradeoff：写进 `AGENTS.md` 更稳定但占上下文，写成 skill 省上下文但不保证每次触发。判断标准是这条信息对所有任务是否都必要，是就放 `AGENTS.md`，否则放 skill。

## 渐进式加载

Skills 的关键设计是 progressive disclosure。典型流程是：

1. Agent 启动时，只看到 skill 的 `name`、`description` 和路径。
2. Agent 根据当前任务判断要不要用某个 skill。
3. 只有被选中的 skill，完整 `SKILL.md` 才会进入上下文。
4. skill 内 `references/`、`scripts/`、`assets/` 也是按需读取。

它的工程价值很直接：把大量“可能用得上的流程”从常驻上下文里挪出去，保留“知道有这些能力”的索引。如果把十个复杂流程都塞进 `AGENTS.md`，模型每次回答都要带着这些内容；很多任务用不上，它们仍然会占 token、分散注意力、干扰当前任务。Skills 让模型先看到“能做哪些事”，等到真的需要时再读完整内容。

懒加载不是免费午餐。因为 skill 不是每次完整加载，模型可能没选中正确 skill、`description` 写得差会触发不了、初始列表可能因为 skill 太多被截断或省略、skill 之间可能冲突、关键规则只放 skill 而模型没加载时也看不到。所以必须每次遵守的规则放 `AGENTS.md`，按条件执行的流程放 skill——前者负责“一定会被看到”，后者负责“细节怎么执行”。

## 一个 Skill 的形态

最小结构是 `my-skill/SKILL.md`。更完整的形态通常包含 `references/`、`scripts/`、`assets/`，按需读取。`SKILL.md` 由 YAML frontmatter 和 Markdown 正文组成，最关键的两个字段是 `name` 和 `description`。`name` 通常和目录名一致；`description` 决定 skill 在自动触发时是否被选中。

`description` 的工程位置比看起来更重要：它不是文档，是路由信号。差的写法是 `description: Helps with docs.`；好的写法是说明什么时候用、用户可能怎么表达、什么时候不要用、关键触发词、任务边界。Codex 文档也明确这一点：初始 skills 列表有上下文预算，skill 太多时描述会被缩短，极端情况下某些 skills 会从初始列表里省略。所以 `description` 的精度直接决定 skill 能否被触发。

## 适合和不适合

适合放进 skill：重复出现的任务流程、项目特有约定、容易忘的边界条件、输出格式要求、验证步骤、可复用脚本的调用方式。

不适合：每次任务都必须遵守的安全规则、太泛的“最佳实践”、一次性任务说明、大段通用教材、模型已经知道的常识。

判断方法是看信息的覆盖范围：所有任务都需要就放 `AGENTS.md`，只有某类任务需要就放 skill。

## Skill、AGENTS.md、Tool 的关系

Tool 是动作能力，是模型可以直接调用的接口。Skill 是做事方法，告诉模型在某个任务下应该按什么顺序、用哪些 tool、产出什么格式。`AGENTS.md` 是常驻规则，定义边界和触发条件。一个常见的组合是：`AGENTS.md` 写“修改文档时必须使用某个 skill”，skill 写具体怎么检查链接、怎么处理双向链接、什么时候跑脚本；`AGENTS.md` 负责保证触发，skill 负责保证细节。

## 相关概念

- [[Prompting]]
- [[Context Engineering]]
- [[Tool Use]]
- [[Prompt Cache]]
- [[Security]]
- [[Evals]]

## 参考资料

- [OpenAI Codex Agent Skills](https://developers.openai.com/codex/skills)：看 progressive disclosure 和 description 路由的具体形态。
- [OpenAI Codex AGENTS.md](https://developers.openai.com/codex/guides/agents-md)：看常驻规则和 instruction chain 的合并方式。
- [Using skills to accelerate OSS maintenance](https://developers.openai.com/blog/skills-agents-sdk)：看一个真实的 skill 组织方式。
- [Agent Skills Specification](https://agentskills.io/specification)：Skill 目录和 frontmatter 的标准规范。
- [Agent Skills Best Practices](https://agentskills.io/skill-creation/best-practices)：description 和触发条件的设计建议。
