# Skills

先说一个容易被包装过头的事实：Skill 本质上还是 prompt。

更准确一点说，skill 是被文件化、结构化、可复用、可按需加载的 prompt。它通常放在一个目录里，核心文件叫 `SKILL.md`。这个文件里写的还是自然语言指令，只是它有固定的元数据、触发方式、目录结构，还可以带脚本和参考资料。

所以不要把 Skills 理解成什么神秘能力。它更像是把“我每次都要重复告诉 Agent 的做事方法”沉淀成一个小模块。

相关概念：

- [[Prompting]]
- [[Context Engineering]]
- [[Tool Use]]
- [[Prompt Cache]]
- [[Security]]
- [[Evals]]

## 为什么不直接写进 AGENTS.md

可以直接写进 `AGENTS.md`，但不总是应该这么做。

`AGENTS.md` 适合放每次任务都应该知道的规则，比如：

- 项目怎么启动
- 测试怎么跑
- 代码风格
- 安全边界
- 哪些事情必须先问用户
- 哪些 skill 在什么情况下必须调用

Skill 适合放不是每次都需要，但一旦需要就应该完整执行的流程，比如：

- 发布前检查
- 文档同步
- 代码审查
- 数据清洗
- 生成周报
- 处理 PDF
- 维护某个特定仓库

区别在于：`AGENTS.md` 更像常驻背景规则，skill 更像按需取用的工作流。

这里有一个真实 tradeoff：

- 写进 `AGENTS.md`：更稳定，模型每次都看得到，但占上下文
- 写成 skill：更省上下文，可以写得更细，但不保证每次都会加载

面试里讲 Skills，最好把这个 tradeoff 说出来。只说“skill 能复用 prompt”太浅。

## 懒加载和渐进式加载

Skills 的关键设计不是“把 prompt 换个地方放”，而是懒加载。

典型流程是：

1. Agent 启动时，只看到 skill 的 `name`、`description` 和路径
2. Agent 根据用户任务判断要不要用某个 skill
3. 只有被选中的 skill，完整 `SKILL.md` 才会进入上下文
4. 如果 skill 里还有 `references/`、`scripts/`、`assets/`，通常也是需要时再读

这叫 progressive disclosure，中文可以理解成“渐进式暴露”。

它的好处很直接：省上下文。

如果你把十个复杂流程都塞进 `AGENTS.md`，模型每次回答都要带着这些内容。很多任务根本用不上这些流程，但它们仍然会占 token，影响注意力，也可能和当前任务互相干扰。

Skills 的设计就是把这些流程放到旁边：先让模型知道“有这个能力”，等真的需要时再加载完整内容。

## 代价是什么

懒加载不是免费午餐。

因为 skill 不是每次都完整加载，所以有几个风险：

- 模型可能没有选中正确 skill
- `description` 写得不好时，skill 可能触发不了
- skills 太多时，初始列表可能被截断或省略
- skill 之间可能冲突
- 关键规则如果只放在 skill 里，模型没加载 skill 时就看不到

所以一个重要原则是：

> 必须每次遵守的规则放 `AGENTS.md`，按条件执行的流程放 skill。

比如：

- “不要泄露 secrets”应该放 `AGENTS.md`
- “处理 PR 时运行某个 review skill”可以放 `AGENTS.md`
- review 的完整步骤、输出格式、检查清单放 skill

这样 `AGENTS.md` 负责触发，skill 负责细节。

## 一个 Skill 的格式

最小结构是：

```text
my-skill/
  SKILL.md
```

更完整一点：

```text
my-skill/
  SKILL.md
  references/
  scripts/
  assets/
```

`SKILL.md` 一般包含两部分：YAML frontmatter 和 Markdown 正文。

```md
---
name: skill-name
description: Explain when this skill should be used.
---

# Skill Name

Write the workflow the agent should follow.
```

最重要的两个字段是：

- `name`：skill 名字，通常要和目录名一致
- `description`：触发条件，告诉 Agent 什么时候该用这个 skill

`description` 很关键，因为自动触发主要靠它。

差的写法：

```yaml
description: Helps with docs.
```

好的写法：

```yaml
description: Use this skill when updating Markdown documentation, checking broken links, or preserving double-bracket note links. Do not use for general web research.
```

好的 `description` 应该说明：

- 什么时候用
- 用户可能怎么表达这个需求
- 什么时候不要用
- 关键触发词
- 任务边界

## 一个具体 Skill 怎么写

假设我们要写一个“维护 Markdown 笔记库”的 skill。

它的目标不是替你回答所有问题，而是告诉 Agent：当它修改这个仓库的笔记时，应该遵守哪些规则。

目录：

```text
markdown-note-maintainer/
  SKILL.md
  scripts/
    check-links.py
```

`SKILL.md`：

````md
---
name: markdown-note-maintainer
description: Use this skill when editing a Markdown note repository, preserving double-bracket note links, checking internal note references, or reorganizing note files. Do not use for general writing tasks.
license: MIT
---

# Markdown Note Maintainer

Use this skill when editing a Markdown note repository.

## Rules

- Internal note relationships must use double-bracket links.
- Ordinary Markdown links are only for external websites.
- Do not rewrite note links into GitHub-style relative links.
- Keep directory names in Chinese unless the name is a technical term.
- Preserve existing note titles unless the user asks to rename them.

## Workflow

1. List the Markdown files related to the requested change.
2. Identify existing note links before editing.
3. Make the smallest content change that satisfies the request.
4. Check whether every internal note link has a matching Markdown file.
5. If files are moved or renamed, update affected note links.
6. Report unresolved links instead of silently guessing.

## Validation

Before finalizing:

- Run `scripts/check-links.py` if it exists.
- Confirm that internal note links still use double-bracket syntax.
- Confirm that external URLs are still ordinary Markdown links.
````

这个 skill 有几个点值得注意：

- 它不是百科，不解释 Markdown 是什么
- 它写的是项目里的具体规则
- 它有触发条件，也有排除条件
- 它把“检查链接”这种机械工作交给脚本
- 它把容易犯错的地方写进 rules

这就是一个有用 skill 的样子。

## 什么适合写进 Skill

适合：

- 重复出现的任务流程
- 项目特有的约定
- 容易忘的边界条件
- 输出格式要求
- 验证步骤
- 常见失败模式
- 可复用脚本的调用方式

不适合：

- 每次任务都必须遵守的安全规则
- 太泛的“最佳实践”
- 一次性任务说明
- 大段通用教材
- 模型本来就知道的常识

一个判断方法：

> 如果这个信息只有某类任务需要，适合放 skill；如果所有任务都需要，放 `AGENTS.md`。

## Skill 和 Prompt 的区别

Skill 是 prompt，但不是普通 prompt。

普通 prompt 是一次性的：

```text
帮我检查这篇文档有没有断链。
```

Skill 是可复用的：

```text
以后每次维护 Markdown 笔记库，都按这个流程检查链接、保留双向链接、报告断链。
```

所以它的重点不是“换个地方存提示词”，而是把重复流程变成可版本管理、可共享、可触发、可测试的模块。

## Skill 和 Tool 的区别

Tool 是动作能力。Skill 是做事方法。

例子：

- `shell` 是 tool
- “发布前按顺序跑 lint、test、build”是 skill
- `web_search` 是 tool
- “调研一个开源项目时整理 README、issues 和最近 release”是 skill

一个 skill 可以指导 Agent 使用多个 tools，但 skill 本身不是 tool。

## Skill 和 AGENTS.md 的区别

可以这样记：

```text
AGENTS.md = 常驻规则
Skill = 按需加载的流程
Tool = 可调用动作
```

更具体：

- `AGENTS.md` 在任务开始前读取，适合放长期有效的项目规则
- skill 先暴露 `name` 和 `description`，完整内容只有选中后才加载
- tool 是实际执行动作的接口

一个比较好的组合方式是：

```text
AGENTS.md：
- 修改文档时必须使用 markdown-note-maintainer skill

SKILL.md：
- 具体怎么检查链接
- 怎么处理双向链接
- 怎么输出结果
- 什么时候运行脚本
```

这样既能保证关键流程会被触发，又不会把完整流程长期塞进上下文。

## 面试怎么讲

如果面试官问：“你了解 Agent 的 Skills 系统吗？”

可以这样答：

> 我理解 Skills 本质上还是 prompt，但它是结构化、文件化、可复用的 prompt。它的关键设计是渐进式加载：系统启动时通常只给模型 skill 的 name、description 和路径，完整 SKILL.md 只有在模型判断需要这个 skill 时才加载。所以 Skills 相比直接写进 AGENTS.md 更省上下文，也能承载更完整的流程、脚本和参考资料。但代价是它不是每次都加载，触发依赖 description 和系统路由。如果某条规则必须每次生效，我不会只放在 skill 里，而会在 AGENTS.md 里写触发规则，再把细节放到 skill。

如果继续追问，可以展开：

- 为什么 `description` 重要：它决定隐式触发是否成功
- 为什么要渐进式加载：减少上下文占用
- 为什么不能什么都放 skill：关键规则可能因为未触发而失效
- 为什么不能什么都放 `AGENTS.md`：上下文会变重，也容易干扰当前任务
- 怎么测试 skill：设计几类任务，看它是否该触发时触发、不该触发时不触发，输出是否稳定

这个回答比“Skills 是可复用 prompt”更有含金量，因为它讲到了系统设计取舍。

## 出处和证据

OpenAI Codex Skills 文档说得很明确：Skills 用 progressive disclosure 管理上下文；Codex 一开始只拿到每个 skill 的 `name`、`description` 和路径，只有决定使用某个 skill 时才读取完整 `SKILL.md`。同一页还说明，初始 skills 列表有上下文预算；skills 很多时，描述会被缩短，极端情况下某些 skills 会从初始列表里省略。

OpenAI 的 `AGENTS.md` 文档则说明，Codex 在开始工作前读取 `AGENTS.md`，并在一次运行开始时构建 instruction chain。它还会从全局、项目根目录一路合并到当前目录，直到达到配置的大小上限。

OpenAI 的实践文章也给了一个很好的组织方式：把每次都该知道的仓库规则和 skill 触发规则放进 `AGENTS.md`，把更细的重复流程放到 repo-local skills 里。文章里还提到，skills 能承载更丰富的 instructions、scripts 和 references，而不会一开始就膨胀 Agent 的上下文。

参考：

- [OpenAI Codex Agent Skills](https://developers.openai.com/codex/skills)
- [OpenAI Codex AGENTS.md](https://developers.openai.com/codex/guides/agents-md)
- [Using skills to accelerate OSS maintenance](https://developers.openai.com/blog/skills-agents-sdk)
- [Agent Skills Specification](https://agentskills.io/specification)
- [Agent Skills Best Practices](https://agentskills.io/skill-creation/best-practices)

## 常见错误

- 把 skill 神秘化，以为它不是 prompt
- 把所有内容都塞进 `AGENTS.md`
- 把必须每次生效的规则只放 skill
- `description` 太泛，导致误触发
- `description` 太窄，导致该触发时不触发
- 写成大而全教程，而不是任务流程
- 没有验证步骤
- 没有测试触发条件
- 装第三方 skill 前不看内容
