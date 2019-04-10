# 第三章 使用 Python 构建 Web 应用

> 本章我们将编写 HTML 页面、JavaScript REST 客户端与微服务交互。主要内容包括:
>
> * 构建 HTML 页面和数据绑定
> * 使用 knockout.js 的 JavaScript REST 客户端
>
> knockout.js 是一个基于 MVVM\(Model View View Model\)的 JavaScript 库，可以独立工作，也可以与其他 JavaScript 库\(如 jQuery 等\)一起使用。通常用于开发桌面 Web 应用程序，它提供了与数据源同步的响应机制，以及数据模型和用户界面之间的双向绑定机制。
> 下面我们讨论 knockout.js 的两个重要概念: 绑定和可观察性\(Observables\)。

## 应用入门

> 首先创建 HTML 模板，在应用程序的根目录下创建一个 template 目录，该目录将存放所有的功能模板。
>
> 首先创建 template\adduser.html 文件:
>
> ```
> <!DOCTYPE html> 
> <html> 
>   <head> 
>     <title>Tweet Application</title> 
>   </head> 
>   <body> 
>     <div class="navbar"> 
>      <div class="navbar-inner"> 
>        <a class="brand" href="#">Tweet App Demo</a> 
>      </div> 
>     </div> 
>    <div id="main" class="container"> 
>
>      Main content here! 
>
>    </div> 
>   <meta name="viewport" content="width=device-width, initial-
>    scale=1.0"> 
>   <link href="http://netdna.bootstrapcdn.com/twitter-
>    bootstrap/2.3.2/css/bootstrap-combined.min.css"
>    rel="stylesheet"> 
>   <script src="http://ajax.aspnetcdn.com/ajax/jquery/jquery- 
>    1.9.0.js"></script> 
>   <script src="http://netdna.bootstrapcdn.com/twitter-
>     bootstrap/2.3.2/js/bootstrap.min.js"></script> 
>   <script src="http://ajax.aspnetcdn.com/ajax/knockout/knockout-
>     2.2.1.js"></script> 
>   </body> 
> </html> 
> ```

## 创建应用程序用户



