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

协议细节、论文推导、API 参数这类内容，如果外部资料已经讲得很好，仓库会保留入口和上下文，不重新写一遍。

## 边界

这里不会重写一套完整的前端、后端、数据库、算法或深度学习教材。传统 CS 仍然重要，HTTP、WebSocket、SSE、数据库、调试、部署、工程质量都会在 AI 项目里出现；仓库只在它们和 AI 系统发生关系时展开，比如模型流式输出、Agent 状态同步、API 调用、长任务取消和重试。

资源页也不是链接排行榜。链接会围绕方向、项目和问题组织，避免变成资料堆。

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

## 阅读方式

在 GitHub 上可以直接读 Markdown。仓库也可以用 [Lumina Note](https://github.com/blueberrycongee/Lumina-Note) 打开，以获得更完整的双向链接和笔记浏览体验。

```bash
git clone https://github.com/blueberrycongee/ai-native-cs-guide.git
```

仓库内的概念链接采用常见的 wikilink 写法，很多笔记工具都支持；GitHub 上则会按普通 Markdown 文本显示。

## 贡献

欢迎贡献，但希望保持克制。

更需要的内容包括：

- 清楚的概念解释
- 高质量外部资料的筛选和背景说明
- 开源项目的结构整理
- 具体工程问题的分析
- 对某个方向边界的整理

不需要的内容包括：

- 没验证过的学习路线
- 只有链接、没有判断的资料堆
- AI 生成后没有认真改过的文字
- 复制官方文档或别人的博客
- 只服务传统面试八股的内容

写文档时请尽量做到：

- 直接说清楚这个内容解决什么问题
- 内部概念沿用仓库现有的笔记链接写法
- 外部资料使用普通 Markdown 链接
- 不把概念文档写成 API reference
- 如果一段内容开始变成大量字段和参数，优先链接外部资料
- 不写空泛口号，不写看起来很完整但没有信息量的段落

## License

本项目采用 MIT License，见 `LICENSE`。
