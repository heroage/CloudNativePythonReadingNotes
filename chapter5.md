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
> 本章的项目需生成的 package.json 内容如下:
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
>
> 生成 package.json 文件后，使用下面的命令安装所有依赖项:
>
> ```
> $ npm install
> ```

## 使用 React 构建 Web 视图

> 首先我们需要创建 React 需要调用的 home 视图。在模板目录下创建 templates/index.html 文件，键入如下内容:
>
> ```
> <!DOCTYPE html> 
> <html> 
>    <head lang="en"> 
>     <meta charset="UTF-8"> 
>     <title>Flask react</title> 
>   </head> 
>   <body> 
>   <div class="container"> 
>     <h1></h1> 
>     <br> 
>     <div id="react"></div> 
>
>   </div> 
>
>    <!-- scripts --> 
>    <script src="https://code.jquery.com/jquery-2.1.1.min.js"></script> 
>    <script src="https://cdnjs.cloudflare.com/ajax/libs/
>      react/15.1.0/react.min.js"></script> 
>    <script src="https://npmcdn.com/react-
>      router@2.8.1/umd/ReactRouter.min.js"></script> 
>    <script src="https://cdnjs.cloudflare.com/ajax/
>      libs/react/15.1.0/react-dom.min.js"></script> 
>    <script src="http://cdnjs.cloudflare.com/ajax/libs/
>      react/0.13.3/JSXTransformer.js"></script> 
>
>   </body> 
> </html>
> ```
>
> 在上面的页面中，我们定义了一个 id 为 react 的 div，下面将基于此来调用 React 主函数执行相关操作。
>
> 创建 static/main.js，加入代码:
>
> ```
> import Tweet from "./components/Tweet"; 
> class Main extends React.Component{ 
>   render(){ 
>     return ( 
>     <div> 
>       <h1>Welcome to cloud-native-app!</h1> 
>     </div> 
>     ); 
>   } 
>  } 
>
>
>  let documentReady =() =>{ 
>   ReactDOM.render( 
>   <Main />, 
>    document.getElementById('react') 
>   ); 
> }; 
>
> $(documentReady);
> ```
>
> 这样我们就定义了 React response 的基本结构。由于我们构建的是一个多视图的应用，因此需要使用一个工具来帮我们把所有资源文件\(包括 JavaScript、图片、字体和 CSS 等等\)打包放到一个独立的包文件中。
>
> 下面我们就用 Webpack 来完成这一工作，继续之前得需要先安装这个工具。
>
> ```
> $ sudo apt install webpack
> ```
>
> **Webpack 作用图解**![](/img/05.webpack作用图解.png)
>
> Webpack 会读入一个独立的 .js 入口文件\(本书中入口文件名为 webpack.config.js 文件中定义的 entry 值，即 ./static/main.js\)，并从入口文件中读取要选取或排除哪些子组件的设置，然后将这些子组件转换成独立的 .js 文件\(在 webpack.config.js 文件 output 中设置\)。\(这段的译文实在是弄得我晕头转向，但本尊也好不到哪去: Webpack, basically, reads a single entry file, which could be the .js file, reads its child components, and then converts them into a single .js file.\)
>
> > 书中对 Webpack 的说明实在是云遮雾罩，所以还是从别处摘抄一段\([https://segmentfault.com/a/1190000006178770](https://segmentfault.com/a/1190000006178770)\)，结合上面的图示就很好理解了:
> >
> > WebPack可以看做是**模块打包机**，它做的事情是: 分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其转换和打包为合适的格式供浏览器使用。
> >
> > Webpack的工作方式是：把你的项目当做一个整体，通过一个给定的主文件（如：index.js），Webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包为一个（或多个）浏览器可识别的JavaScript文件。
>
> 以下为 webpack.config.js 的配置:
>
> ```
> module.exports = {
>     entry: "./static/main.js",
>     output: {
>         path: __dirname + "/static/build/",
>         filename: "bundle.js"
>     },
>     resolve: {
>       extensions: ['.js', '.jsx']
>     },
>     module: {
>         rules: [
>             { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader", query:{presets:['react','es2015']} }
>         ]
>     }
> };
> ```
>
> 两处修正修正:
>
> ```
> 本尊中可能因版本原因有两处报错，修改后正常:
> resolve: {
>     extensions: ['', '.js', '.jsx']
>     
> module: {
>     loaders: [
> ```



