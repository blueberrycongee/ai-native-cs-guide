# Goal

这个文件是完善 `AI Native CS Guide` 的目标文档。

后续如果用户零碎补充想法，先把想法整理进这个文件，再根据这个文件推进仓库内容。不要把聊天里的临时判断散落在各个文档里。

## 仓库目标

这个仓库面向中国计算机学生、自学者和想转向 AI Native 方向的人。

它要解决的问题不是“从零系统学习计算机科学”，而是：

> 如果一个学生想尽快进入 AI 相关方向，做出真实项目，并理解 AI 行业里反复出现的核心概念、工具和工程方法，他应该按什么路径学习？

仓库最终应该帮助读者做到：

- 判断自己适合先走哪条 AI 相关路径
- 知道每条路径最重要的概念、项目和信息源
- 区分必须先学的公共基础和可以遇到再补的细节
- 通过项目建立能力，而不是只收藏资料
- 避免被培训班、八股、营销词和泛泛而谈的 AI 文章带偏

## 核心方法论

### 1. 按路径组织，而不是按概念堆叠

仓库的主线应该是具体路径，例如 Agent、RAG、AI 工具、AI 产品工程、AI Research Engineering 等。

概念不是越多越好。只有当某个概念能帮助读者完成路径里的判断、项目或代码理解时，才值得写。

### 2. 公共基础要抽出来

有些内容不属于任何单一路径，例如 Transformer、token、embedding、context window、inference、LLM API、RAG、evals、security、cost。

这些内容如果放进某个路径目录，会让路径变成杂物间。公共内容应该放到公共目录里，再由不同路径按需链接。

### 3. 文档要有学习决策价值

一篇概念文档不应该只是定义加几个 bullet。

它至少要回答：

- 为什么这个概念值得学
- 对哪个路径重要
- 入门要学到什么程度
- 哪些细节可以先跳过
- 它在真实项目或代码里会怎么出现
- 常见误解是什么
- 有哪些靠谱的一手资料或项目可以看

如果一个概念必须深入代码才能讲清楚，就不要先写成抽象文档。可以先留空、移走，或者放进具体项目分析里。

### 4. Learn by projects

这个仓库不应该变成百科。

每个方向都应该逐渐落到项目路线：

- 做什么项目
- 项目验证什么能力
- 会遇到哪些概念
- 哪些地方最容易做成 demo 而不是产品
- 如何判断项目是否真的可用

概念文档服务于项目路线，不反过来支配仓库结构。

### 5. Link, do not rewrite

已有的高质量资料、论文、官方文档、开源项目，不应该在仓库里重写一遍。

仓库应该做的是筛选、排序和解释：

- 这个资源为什么值得看
- 先看哪部分
- 不需要看哪部分
- 它适合哪个学习阶段
- 它和本仓库其他内容是什么关系

## 目标信息架构

当前结构还在调整中。先按下面的方向收敛。

```text
README.md
GOAL.md

foundations/
  概念/
    Transformer.md
    Attention.md
    Token.md
    Embedding.md
    Context Window.md
    Inference.md

ai-systems/
  概念/
    LLM.md
    Prompting.md
    Context Engineering.md
    RAG.md
    Evals.md
    Security.md
    Cost.md
    Prompt Cache.md

agents/
  Agent.md
  Agent 信息来源.md
  Agent 开源项目.md
  概念/
    Agent Loop.md
    ReAct.md
    Tool Use.md
    Memory.md
    Skills.md
    Single Agent vs Multi-Agent.md

workflow/
products/
projects/
resources/
```

这个结构不是最终定案，但有几个当前决策：

- `agents/概念` 只放 Agent 特有或强相关概念
- `Cost`、`Evals`、`Security` 这类横切问题不属于 Agent 核心目录
- `AI Product` 不属于 Agent 核心目录
- `Workflow` 和 `Human in the Loop` 更像流程或产品化内容，不放在 Agent 核心概念里
- `State` 先不作为独立概念文档，因为它更适合结合具体代码和系统实现讲
- `Skills.md` 当前可以保留，因为它已经有比较具体的方法论和系统设计取舍

## 内容质量标准

写文档时遵循这些标准：

- 不写 AI 味很重的空泛总结
- 不为了显得完整而列概念
- 不把所有相关概念都塞进一个方向目录
- 不写传统培训班式路线图
- 不把“看过资料”伪装成“掌握能力”
- 不推荐没有判断标准的资源清单
- 不把 demo 当成真实项目
- 不写无法指导读者做选择的解释

一篇文档如果暂时写不好，可以留空或只写结构，不要填充低质量内容。

## 后续协作方式

用户可能会零碎补充想法。处理这些输入时：

1. 先判断它是目标、原则、目录结构、内容标准、具体选题，还是待讨论问题
2. 把稳定判断整理进 `GOAL.md`
3. 如果只是临时想法，放进“待处理问题”
4. 如果和已有判断冲突，明确指出冲突，不要强行合并
5. 每次独立整理完成后提交一次

不要急着补正文。先把目标、结构和质量标准稳定下来，再开始批量写文档。

## 待处理问题

- 是否新增 `foundations/`，专门放 Transformer、Attention、token、embedding 等公共基础
- `ai-systems/` 是否是合适命名，还是应该叫 `common/`、`core/`、`ai-core/`
- 每条就业路径应该如何命名
- Agent 方向是否先只保留 `Skills.md`，其它概念页后续重写
- README 的目录规划需要和新的目标结构同步
