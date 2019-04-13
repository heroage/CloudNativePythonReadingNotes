# 第五章 使用 React 构建 Web 视图

> 本章重点介绍如何使用 React 构建前端页面，并将这些页面与后端集成起来，形成一个完整的应用程序。主要内容包括:
>
> * 配置 React 环境
> * 创建用户验证面板
> * 集成 React 和后端 API

## 理解 React

> 简单地说，React 是应用程序的 UI 层，它是一个 JavaScript 库，用于快速方便地构建用户界面。React 可以为应用的每种状态创建精美的 Web 视图。
>
> 首先来看一下 React 的概念/要点:
>
> * **组件**: 所有的 HTML 和 JavaScript 集合称为组件。React 提供了一些钩子，使用 JavaScript 来生成 HTML 页面。这里需要着重指出的是，React 作为控制器，可以为各种状态的应用生成不同的 Web 页面。
> * **Props for static version in React**: 使用 HTML 时，通常要编写大量的前端代码，多数情况下这些代码都是不断的重复。React 解决这个问题，它使用 props 保持数据状态，并将父节点的属性值传递给子节点。
>
> * **识别最小状态**: 要正确构建应用程序，首先要理清应用程序有哪些最小可变状态。也就是说，当应用程序处于不同状态时，需要维护哪些用户状态。
>
> * **识别活动状态**: React 的数据流是沿组件层次单向流动的。组件基于状态生成 HTML 文本，我们需要了解每个这样的组件。同时，我们也需要了解在组件层次级别状态是如何改变的。
>
> * **React-DOM\(React-Document Object Model\)**: react-dom 提供了一种基于 DOM 规范的 React 的访问方式。\(此处看本尊的原文也还是一头雾水，还是说人话的感觉比较好\)

## 配置 React 环境

> 初始化 React 环境需要 node.js 库的支持。

### 安装 node

> 略……

### 创建 package.json

> package.json 是应用程序的元数据配置，其中包含需要为应用程序安装的完整的库/依赖关系。其最大优点是，可以方便地重新构建，这样就简化了与其他开发者的分享。创建自定义的 package.json 的方法有很多种。
>
> 下面是 package.json 文件所需的最简信息:
>
> ```
> {
>   "name": "my-twitter-package",
>   "version": "1.0.0"
> } 
> ```
>
> 使用下面的命令构建 packag.json 模板:
>
> ```
> $ npm init
> ```
>
> 该命令会询问一些信息，包括包名称、版本、描述等等，并根据这些信息生成 package.json 文件。可以加上参数 -y 或 --yes，让 npm 使用缺省值。
>
> npm init 生成的 package.json 结构大致如下:
>
> ```
> { 
>   "name": "twitter", 
>   "version": "1.0.0", 
>   "description": "Twitter App", 
>   "main": "index.js", 
>   "dependencies": { 
>     "babel-loader": "^6.4.1", 
>     "fbjs": "^0.8.11", 
>     "object-assign": "^4.1.1", 
>     "react": "^15.4.2", 
>     "react-dev": "0.0.1", 
>     "react-dom": "^0.14.7", 
>     "requirejs": "^2.3.3" 
>   }, 
>   "devDependencies": { 
>    "babel-core": "^6.4.5", 
>    "babel-loader": "^6.2.1", 
>    "babel-preset-es2015": "^6.3.13", 
>    "babel-preset-react": "^6.3.13", 
>    "webpack": "^1.12.12"
>   }, 
>   "scripts": { 
>     "test": "echo \"Error: no test specified\" && exit 1" 
>   }, 
>   "author": "Manish Sethi", 
>   "license": "ISC" 
> }
> ```



