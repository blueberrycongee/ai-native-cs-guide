# Streaming Response

Streaming response 是边生成边返回结果的响应方式。AI 应用里，它通常用来展示模型正在输出的内容。

它不是前端动画。真正的 streaming 需要模型、后端、网络层和前端都支持逐步传输。

相关概念：

- [[Inference]]
- [[HTTP]]
- [[SSE]]
- [[WebSocket]]
- [[Context Window]]

## 为什么要学

大模型推理可能很慢。用户通常不愿意等几十秒后一次性看到完整答案。

流式输出能改善体验，也能暴露更多状态：

- 首 token 更早出现
- 用户可以提前判断方向是否对
- 长任务可以显示工具调用进度
- 用户取消时可以及时停止后续成本

但 streaming 不能让模型本身变聪明，也不能自动解决超时和错误处理。

## 学到什么程度

先掌握：

- 首 token 延迟和完整响应延迟的区别
- 后端如何把上游模型流转发给前端
- 为什么需要取消、错误事件和完成事件
- 什么情况下用 [[SSE]]，什么情况下用 [[WebSocket]]

不需要一开始研究所有传输协议。把一条链路做通、做稳，再考虑抽象。

## 在项目里怎么出现

一个典型链路：

1. 浏览器向后端发起请求
2. 后端调用模型并开启 stream
3. 模型逐步返回 token 或事件
4. 后端通过 [[SSE]] 转发
5. 前端逐步渲染并允许取消

如果任一层把响应缓存起来，用户看到的就不是 streaming，而是一次性返回。

## 资料

- [MDN Streams API](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API)：理解浏览器流式处理。
- [OpenAI API streaming](https://platform.openai.com/docs/api-reference/responses/create)：看模型 API 的 streaming 参数和事件。
