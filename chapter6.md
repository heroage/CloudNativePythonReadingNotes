# 第六章 使用 Flux 来构建 UI 应对应用拓展

> 前一章，我们为应用程序创建了 Web 视图，并把前后端集成起来。
>
> 本章着重构建前端部分。理想情况下，每个模块只应负责一件事情。而前一章中的模块所做工作过多，除了呈现视图外，还要发送 API 请求、接收处理和格式化响应等等。
>
> 本章包括以下主要内容:
>
> * 了解 Flux
> * 在 React 中实现 Flux

## Flux 介绍

> Flux 是 Facebook 创建的一种模式，使用 React 创建一致、稳定的 Web 应用程序。React 在其中的作用并非是管理数据，它只是接收从 props 和组件传递的数据，再把数据交由组件处理。
>
> React 不会告诉你如何获取组件，或是把数据存在哪里，所以 React 只是被称为视图层。React 没有像 Angular 或 backbone 这样的框架，因此就需要 Flux 来处理这样的事务。Flux 并不是框架，而是帮忙构建视图的模式。
>
> **Flux模式视图**
>
> ## ![](/img/06.Flux模式.png)

### Flux 概念

> * **Action**: 组件通过动作与 API 进行交互并更新。当动作被推送到分派器后，可能会产生多个动作。
> * **Dispatcher**: 将流入的所有事件分别派发给每个订阅者，通常是数据存储。
> * **Store**: Flux 的重要组成部分。相关组件总是在监听来自存储的所有变更，一旦发生变更，都会触发相关事件。
> * **Componet**: 用于保存动作名称。





