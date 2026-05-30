# AI Native CS Guide

这是一个给中国计算机学生和自学者看的 AI 学习笔记库。

它不是传统 CS 路线图，也不是资料收藏夹。这个仓库整理 AI 相关方向里的信息来源、核心概念、开源项目和工程问题，让分散资料之间的关系更清楚。

方向页说明边界，信息来源页收集资料入口，项目页记录系统形态。更具体的问题会通过概念文章、工程文章和内部链接继续展开。

## 这个仓库做什么

这个仓库关心的问题很具体：

> AI 相关方向里有哪些核心问题、资料入口、代表项目和工程模块？

内容大致分成几类：

1. AI 的公共基础，比如 [[Transformer]]、[[Attention]]、[[Token]]、[[Embedding]]
2. AI 行业方向地图，比如 [[Agent]]、[[AI Infra]]、[[Pre-Train]]、[[Post-Train]]、[[RL]]
3. AI 应用工程基础，比如 [[HTTP]]、[[WebSocket]]、[[SSE]]、[[Streaming Response]]
4. AI 系统常用能力，比如 [[LLM]]、[[Prompting]]、[[Context Engineering]]、[[RAG]]
5. 具体方向的信息来源、开源项目和工具生态
6. 经典论文深挖，把关键论文连接到概念和工程实现

有些知识值得自己写，有些不值得。协议细节、论文推导、API 参数这类内容，如果外部资料已经讲得很好，我们会直接链接过去。这个仓库更应该做的是筛选入口、整理关系和补足上下文。

## 这个仓库不做什么

这里不会重写一套完整的前端、后端、数据库、算法或深度学习教材。

传统 CS 仍然重要。HTTP、WebSocket、SSE、数据库、调试、部署、工程质量，这些都会在 AI 项目里出现。但我们只会围绕 AI 场景讲它们，比如模型流式输出、Agent 状态同步、API 调用、长任务取消和重试。

这个仓库也不打算做：

- 大而全的资料列表
- 培训班式学习路线
- 为简历关键词堆出来的项目清单
- 替读者排序的资源推荐
- 把别人的文档重新抄一遍的二手教程
- 课程式项目作业

## 仓库结构

目录大致是：

```text
01-foundations/
02-ai-directions/
03-engineering-basics/
04-ai-systems/
05-projects/
06-papers/
90-resources/
```

`01-foundations/` 放 AI 方向反复用到的基础概念。`02-ai-directions/` 放 Agent、AI Infra、Pre-Train、Post-Train、RL 等方向。`03-engineering-basics/` 放 AI 应用会频繁遇到的工程基础。`04-ai-systems/` 放 RAG、eval、prompt、context engineering 等系统能力。`05-projects/`、`06-papers/` 和 `90-resources/` 分别收项目、论文和跨方向资料。

重点方向通常会有这些页面：

- 方向总览：说明讨论范围和核心问题。
- 信息来源：整理官方文档、论文、release note、技术博客、issue、discussion 等入口。
- 开源项目：列项目入口、系统形态和相关主题。
- 框架和工具：按问题域整理工具生态。
- 概念文章：把具体机制、工程边界和实现问题讲清楚。

## 用 Lumina Note 阅读

这个仓库是 Markdown 笔记库，内部链接使用双括号：

```md
[[Transformer]]
[[Agent]]
[[Context Engineering]]
```

在 GitHub 上可以直接读 Markdown。但如果想要更好的双向链接和笔记浏览体验，可以把仓库 clone 到本地，用 [Lumina Note](https://github.com/blueberrycongee/Lumina-Note) 打开。

```bash
git clone https://github.com/blueberrycongee/ai-native-cs-guide.git
```

Lumina Note 是一个 Markdown 笔记项目，支持双向链接，适合阅读这种不是线性教材的知识库。

## 贡献

欢迎贡献，但希望保持克制。

优先贡献：

- 清楚的概念解释
- 高质量外部资料的筛选和背景说明
- 开源项目的结构整理
- 具体工程问题的分析
- 对某个方向边界的整理

不收：

- 没验证过的学习路线
- 只有链接、没有判断的资料堆
- AI 生成后没有认真改过的文字
- 复制官方文档或别人的博客
- 只服务传统面试八股的内容

写文档时请尽量做到：

- 直接说清楚这个内容解决什么问题
- 内部概念使用 `[[...]]` 双向链接
- 外部资料使用普通 Markdown 链接
- 不把概念文档写成 API reference
- 如果一段内容开始变成大量字段和参数，优先链接外部资料
- 不写空泛口号，不写看起来很完整但没有信息量的段落

更具体的目标和写作规则见 [[GOAL]]。

## License

本项目采用 MIT License，见 `LICENSE`。
