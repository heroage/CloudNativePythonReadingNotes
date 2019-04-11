# 第三章 使用 Python 构建 Web 应用

> 本章我们将编写 HTML 页面、JavaScript REST 客户端与微服务交互。主要内容包括:
>
> * 构建 HTML 页面和数据绑定
> * 使用 knockout.js 的 JavaScript REST 客户端
>
> knockout.js 是一个基于 MVVM\(Model View View Model\)的 JavaScript 库，可以独立工作，也可以与其他 JavaScript 库\(如 jQuery 等\)一起使用。通常用于开发桌面 Web 应用程序，它提供了与数据源同步的响应机制，以及数据模型和用户界面之间的双向绑定机制。  
> 下面我们讨论 knockout.js 的两个重要概念: 绑定和可观察性\(Observables\)。

## 应用入门

> 首先创建 HTML 模板，在应用程序的根目录下创建一个 templates 目录，该目录将存放所有的功能模板。
>
> 首先创建 templates\adduser.html 文件:
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

## 使用应用创建用户

> 在开始 Web 页面之前，首先在 app.py 中加入用户路由:
>
> ```
> from flask import render_template
>
> @app.route('/adduser')
> def adduser():
>     return render_template('adduser.html')
> ```
>
> 然后，修改 templates\adduser.html:
>
> ```
> <html> 
>   <head> 
>     <title>Twitter Application</title> 
>   </head> 
>   <body> 
>    <form > 
>      <div class="navbar"> 
>       <div class="navbar-inner"> 
>         <a class="brand" href="#">Tweet App Demo</a> 
>       </div> 
>     </div> 
>     <div id="main" class="container"> 
>      <table class="table table-striped"> 
>        Name: <input placeholder="Full Name of user" type "text"/> 
>        </div> 
>        <div> 
>          Username: <input placeholder="Username" type="username">
>          </input> 
>        </div> 
>        <div> 
>          email: <input placeholder="Email id" type="email"></input> 
>        </div> 
>        <div> 
>          password: <input type="password" placeholder="Password">  
>          </input> 
>        </div> 
>         <button type="submit">Add User</button> 
>       </table> 
>     </form> 
>    <script src="http://cdnjs.cloudflare.com/ajax/libs/
>     jquery/1.8.3/jquery.min.js"></script> 
>   <script src="http://cdnjs.cloudflare.com/ajax/libs/knockout
>     /2.2.0/knockout-min.js"></script> 
>   <link href="http://netdna.bootstrapcdn.com/twitter-
>    bootstrap/2.3.2/css/bootstrap-combined.min.css"
>    rel="stylesheet"> 
>   <!-- <script src="http://ajax.aspnetcdn.com/ajax/jquery/jquery-
>    1.9.0.js"></script> --> 
>  <script src="http://netdna.bootstrapcdn.com/twitter- 
>    bootstrap/2.3.2/js/bootstrap.min.js"></script> 
>  </body> 
> </html> 
> ```
>
> #### 使用 Observable 和 AJAX
>
> 我们可以使用 AJAX 从 RESTful API 获取数据。在所有应用 Obervable 之处，都会根据 ViewModel 的定义，自动跟踪数据变化，并作出处理。
>
> 在 static 目录下创建 app.js 文件，其中声明了 Observable，具体代码如下:
>
> ```
> function User(data) {
>     this.id = ko.observable(data.id);
>     this.name = ko.observable(data.name);
>     this.username = ko.observable(data.username);
>     this.email = ko.observable(data.email);
>     this.password = ko.observable(data.password);
> }
>
> function UserListViewModel() {
>     var self = this;
>     self.user_list = ko.observableArray([]);
>     self.name = ko.observable();
>     self.username= ko.observable();
>     self.email= ko.observable();
>     self.password= ko.observable();
>
>     self.addUser = function() {
> 	self.save();
>         self.name("");
> 	self.username("");
> 	self.email("");
>         self.password("");
>     };
>
>     self.save = function() {
> 	return $.ajax({
> 	    url: '/api/v1/users',
> 	    contentType: 'application/json',
> 	    type: 'POST',
> 	    data: JSON.stringify({
> 		'name': self.name(),
> 		'username': self.username(),
>                 'email': self.email(),
>                 'password': self.password()
> 	    }),
> 	    success: function(data) {
>                  alert("success")
> 		      console.log("Pushing to users array");
> 		      self.push(new User({ name: data.name, username: data.username,email: data.email ,password: data.password}));
> 		      return;
> 	    },
> 	    error: function() {
> 		return console.log("Failed");
> 	    }
> 	});
>     };
> }
>
> ko.applyBindings(new UserListViewModel());
> ```
>
> 当在页面提交内容时，app.js 将收到一个请求，这行代码会处理该请求:
>
> ```
> ko.applyBindings(new UserListViewModel());
> ```
>
> 这行代码将创建 UserListViewModel 对象，并将其传递给:
>
> ```
> self.addUser = function() {
>     self.save();
>     self.name("");
>     self.username("");
>     self.email("");
>     self.password("");
> }
> ```
>
> 上面的 addUser 函数调用 self.save 方法传递数据对象，save 函数使用 ajax 对 RESTful API 进行调用，



