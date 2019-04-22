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
> 创建 static/Tweet.js 文件，内容如下:
>
> ```
> export default class Tweet extends React.Component {
>   render() {
>     return (
>       <div className="row">
>         <nav>
>           <form>
>             <div>
>               <textarea ref="tweetTextArea" />
>               <label>How you doing?</label>
>               <button>Tweet now</button>
>             </div>
>           </form>
>         </nav>
>       </div>
>     );
>   }
> }
> ```
>
> 这样我们就定义了 React response 的基本结构。由于我们构建的是一个多视图的应用，因此需要使用一个工具来帮我们把所有资源文件\(包括 JavaScript、图片、字体和 CSS 等等\)打包放到一个独立的包文件中。

## webpack\(补充\)

> 本尊对 webpack 的讲解蜻蜓点水，让俺无可奈何、一头雾水。因此，继 REACT 后，还是得找点 webpack 的东东恶补一下。
>
> > 从各处得来的信息理解， webpack 最大的作用就是为了解决各类资源\(js、css……\)之间混乱的引用关系。举个例子来说，如果一个 index.html 需要引用 a.js，而 a.js 又依赖 b.js，则 index.html 需要同时引入 a.js 和 b.js。如果这种依赖关系树很复杂，无疑对前端人员来说是一种灾难。另外，还有如果 a.js 和 b.js 之间如果要共享信息，目前可行、方便的方式是通过 windows.variable 这类全局变量来传递，这就完全破坏了模块间的封装性，为后续的开发带来了无尽的混乱。而 webpack 通过将众多互相依赖的资源\(如 .js、.css 等等\)打包成一个或几类资源文件，在前端文件引用时，只需要引入一个打包文件\(如 bundle.js\)即可，很好地解决了上述问题。
>
> 找到个一个深入浅出的 webpack 视频教程，虽然是收费的，但前面几段免费的内容也足够对 webpack  有些认识: [https://biaoyansu.com/21.x](https://biaoyansu.com/21.x)
>
> 以下的概念主要摘自 [https://www.webpackjs.com/concepts/](https://www.webpackjs.com/concepts/)，也对其他来源的信息进行了稍许整理:
>
> 本质上，_webpack _是一个现代 JavaScript 应用程序的_静态模块打包器\(module bundler\)_。当 webpack 处理应用程序时，它会递归地构建一个_依赖关系图\(dependency graph\)_，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 _bundle_。
>
> **webpack 的四个核心概念**:
>
> * entry
> * output
> * loader
> * plugins
>
> > **entry**: 表示webpack编译的入口文件，通常由html通过script标签引入。
> >
> > **output**: webpack 编译的输出文件\(bundle\)
> >
> > _**loader**: _让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）。loader 可以将所有类型的文件转换为 webpack 能够处理的有效[模块](https://www.webpackjs.com/concepts/modules)，然后你就可以利用 webpack 的打包能力，对它们进行处理。\(loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL，或者将CSS文件、markdown等非 js 文件转换为 js 文件并`require`进来\)
> >
> > **plugins**: loader 的作用是将相关的非 js 文件转换成 webpack 能处理的有效模块，即用来对输入标准化，而 plugins 的作用则是定制 webpack 的编译输出。想要使用一个插件，你只需要 require\(\) 它，然后把它添加到 plugins 列表中。
> >
> > 以下面的 webpack.config.js 文件内容为例:
> >
> > ```
> > const HtmlWebpackPlugin = require('html-webpack-plugin');
> > const webpack = require('webpack');
> > const path = require('path');
> >
> > module.exports = {
> >     entry: "./static/main.js",
> >     output: {
> >         path: path.resolve(__dirname + "/static/build/"),
> >         filename: "bundle.js"
> >     },
> >     resolve: {
> >         extensions: ['.js', '.jsx']
> >     },
> >     module: {
> >         rules: [
> >               { 
> >                 test: /\.js$/, 
> >                 loader: 'babel-loader' 
> >               }, 
> >               { 
> >                 test: /\.(png|jpg|gif|svg)$/,
> >                 loader: 'file-loader',
> >                 options: { name: '[name].[ext]?[hash]'}
> >               }
> >               { 
> >                  test: /\.txt$/, use: 'raw-loader' 
> >               }
> >         ]
> >     }
> >     plugins: [
> >       //压缩js插件
> >       new webpack.optimize.UglifyJsPlugin(),
> >       //以index.html文件为模板生成html5新文件
> >       new HtmlWebpackPlugin({template: './src/index.html'})
> >     ]
> > };
> > ```
> >
> > 其中的 module/rules 是一个 list 变量，其中的 loader 属性表示使用哪个装载器，而 test 属性的值是正则表达式，用于告知 webpack 编译器，当遇到 require 或 import 语句时，只要其路径可以匹配正则表达式\(此处为 /.js$/\)时，就得使用 loader 属性对应的装载器\(此处为 babel-loader\)处理\(babel 是一个 js 编译器，可以将 es6+ 转换成 es5，以便现在的浏览器能够正确执行编写的 JavaScript\)。
>
> 下面我们就用 Webpack 来完成这一工作，继续之前得需要先安装这个工具。
>
> ```
> $ sudo apt install webpack
> ```
>
> 或使用 npm 安装
>
> ```
> # 全局安装
> $ npm install -g webpack
>
> # 安装到你的项目目录
> $ npm install --save-dev webpack
> # 或
> $ npm i -D
> # 这时的 webpack 在 ./node_modules/.bin 目录下
> ```
>
> **Webpack 作用图解**![](/img/05.webpack作用图解.png)
>
> Webpack 会读入一个独立的 .js 入口文件\(本书中入口文件名为 webpack.config.js 文件中定义的 entry 值，即 ./static/main.js\)，并从入口文件中按相关配置读取子组件，然后将这些子组件转换成独立的 .js 文件\(在 webpack.config.js 文件 output 中设置\)。
>
> > 书中对 Webpack 的说明实在是云遮雾罩，所以还是从别处摘抄一段\([https://segmentfault.com/a/1190000006178770](https://segmentfault.com/a/1190000006178770)\)，结合上面的图示就很好理解了:
> >
> > WebPack可以看做是**模块打包机**，它做的事情是: 分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其转换和打包为合适的格式供浏览器使用。
> >
> > Webpack的工作方式是：把你的项目当做一个整体，通过一个给定的主文件（如：index.js），Webpack将从这个文件开始找到你的项目的所有依赖文件，使用 loaders 引入它们，最后打包为一个（或多个）浏览器可识别的 JavaScript 等文件。
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
> 原文件为:
> resolve: {
>     extensions: ['', '.js', '.jsx']
>     
> module: {
>     loaders: [
>     
> 修改后:
> resolve: {
>     extensions: ['.js', '.jsx']
>     
> module: {
>     rules: [
> ```
>
> 完成上面的配置使用下面的命令构建第一个 Web 视图:
>
> ```
> $ webpack -d
> ```
>
> 使用 -d 参数开启了调试模式，这将生成一个名为 bundle.js.map 的日文件，其中记录了 webpack 的所有活动信息。由于我们后面可能会多次修改入口文件\(./static/main.js\)，每次都需要重新构建应用，因此可以使用 --watch\(-w\) 参数，以持续监听 main.js 的变更
>
> ```
> $ webpack -d -w
> ```
>
> > 此处由于我的 babel 版本与本尊不同，所以在执行该命令时报错:
> >
> > ```
> > ERROR in ./static/main.js
> > Module build failed (from ./node_modules/babel-loader/lib/index.js):
> > TypeError: Cannot read property 'babel' of undefined
> >     at Object.module.exports (~/桌面/CloudNativePython/node_modules/babel-loader/lib/index.js:103:36)
> > ```
> >
> > 经过搜索，发现只需修改 ~/桌面/CloudNativePython/node\_modules/babel-loader/lib/index.js 文件中的第103行的代码即可:
> >
> > ```
> > 原文件:
> > var globalOptions = this.options.babel || {};
> >
> > 修改为:
> > var globalOptions = this.options && this.options.babel || {};
> > ```
>
> 完成以上动作后，需要做两项工作记得修改 app.py 中的路由:
>
> ```
> @app.route('/index')
> def index():
>     return render_template('index.html')
> ```
>
> 另外，修改 ./static/index.html，插入一行:
>
> ```
> <script type="text/javascript" src="./static/build/bundle.js"></script>
> ```
>
> 完成以上这些修改之后，就可以通过 [http://localhost:5000/index](http://localhost:5000/index) 来访问构建的页面了。
>
> 接下来继续修改 ./static/main.js 文件，将其中 render 函数的 &lt;Main /&gt; 修改为 &lt;Tweet /&gt;:
>
> ```
> import Tweet from "./components/Tweet";
> class Main extends React.Component{
>   render(){
>     return (
>       <div>
>         <h1>Welcome to cloud-native-app!</h1>
>       </div>
>     );
>   }
> }
>
>
> let documentReady =() =>{
>   ReactDOM.render(
>     <Tweet />,
>     document.getElementById('react')
>   );
> };
>
> $(documentReady);
> ```
>
> 修改完再访问 [http://localhost:5000/index，会看到一个很简陋的页面。为了让页面更加的赏心悦目，我们引入几个](http://localhost:5000/index，会看到一个很简陋的页面。为了让页面更加的赏心悦目，我们引入几个) Materialize 的 CSS，在 index.html 的 script 段中插入以下代码:
>
> ```
>     <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.98.1/css/materialize.min.css">
>     <script src="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.98.1/js/materialize.min.js"></script>
> ```
>
> 并修改 ./static/components/Tweet.js，为页面元素增加样式:
>
> ```
> export default class Tweet extends React.Component {
>   render() {
>     return (
>       <div className="row">
>         <nav>
>           <form>
>             <div className="row">
>               <textarea ref="tweetTextArea" className="materialize-textarea" />
>               <label>How you doing?</label>
>               <button className="btn waves-effect waves-light right">Tweet now<i className="material-icorns right">send</i></button>
>             </div>
>           </form>
>         </nav>
>       </div>
>     );
>   }
> }
> ```
>
> 到这里，可能是由于本尊要求的环境与我的环境不一致，导致代码没法运行。在此处再做耽搁只能是空耗时光，后半段先知其意，容后再补全后面的代码和流程吧。



