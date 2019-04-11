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
> 首先创建 templates/adduser.html 文件:
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
> 然后，修改 templates/adduser.html:
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
>     self.save();
>         self.name("");
>     self.username("");
>     self.email("");
>         self.password("");
>     };
>
>     self.save = function() {
>     return $.ajax({
>         url: '/api/v1/users',
>         contentType: 'application/json',
>         type: 'POST',
>         data: JSON.stringify({
>                 'name': self.name(),
>                 'username': self.username(),
>                 'email': self.email(),
>                 'password': self.password()
>         }),
>         success: function(data) {
>              alert("success")
>                   console.log("Pushing to users array");
>                   self.push(new User({ name: data.name, username: data.username,email: data.email ,password: data.password}));
>                   return;
>         },
>         error: function() {
>             return console.log("Failed");
>         }
>     });
>     };
> }
>
> ko.applyBindings(new UserListViewModel());
> ```
>
> 当在页面提交内容时，static/app.js 将收到一个请求，这行代码会处理该请求:
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
> 上面的 addUser 函数调用 self.save 方法传递数据对象，save 函数 进行 AJAX RESTful API 调用，并从页面中收集数据执行 POST。完成之后，清除页面内容。
>
> 要将这里的 app.js 添加到页面脚本中，才能实现双向数据绑定。因此需要在 templates/adduser.html 的 script 标签中添加一行:
>
> ```
> <script src="{{ url_for('static', filename='app.js') }}"></script>
> ```

#### 绑定数据到 adduser 模板

> 数据绑定功能对实现数据与 UI 的对应非常方便。如果不使用 observable，UI 的属性仅会在首次加载时被关联。这样，UI 就无法根据底层数据的更新而自动更新。为了避免这种情况，记得绑定时一定要引用 observable 属性。
>
> 下面我们对数据和表单中的字段进行绑定，需修改 adduser.html:
>
> ```
> <form data-bind="submit: addUser"> 
>  <div class="navbar"> 
>    <div class="navbar-inner"> 
>        <a class="brand" href="#">Tweet App Demo</a> 
>    </div> 
>  </div> 
>  <div id="main" class="container"> 
>   <table class="table table-striped"> 
>    Name: <input data-bind="value: name" placeholder="Full Name of user" type "text"/> 
>  </div> 
>  <div> 
>    Username: <input data-bind="value: username" 
>    placeholder="Username" type="username"></input> 
>  </div> 
> <div> 
>   email: <input data-bind="value: email" placeholder="Email id" 
>   type="email"></input> 
> </div> 
> <div> 
>    password: <input data-bind="value: password" type="password" 
>    placeholder="Password"></input> 
> </div> 
>    <button type="submit">Add User</button> 
>  </table> 
> </form>
> ```
>
> 为了验证从页面中添加用户是否成功，我们直接在页面中显示数据库中的用户列表。
>
> 首先，在 static/app.js 中加入代码:
>
> ```
> $.getJSON('/api/v1/users', function(userModels) {
>     var t = $.map(userModels.user_list, function(item) {
>         return new User(item);
>     });
>     self.user_list(t);
> });
> ```
>
> 在 templates/adduser.html 中加入如下代码:
>
> ```
> <ul data-bind="foreach: user_list, visible: user_list().length > 0"> 
>   <li> 
>     <p data-bind="text: name"></p> 
>     <p data-bind="text: username"></p> 
>     <p data-bind="text: email"></p> 
>    <p data-bind="text: password"></p> 
>  </li> 
> </ul>
> ```

### 用户创建 tweet

> 首先，在 app.py 中增加路由:
>
> ```
> @app.route('/addtweets')
> def addtweets():
>   return render_template('addtweets.html')
> ```
>
> 然后，在 templates/addtweets.html 中创建一个表单，以便用户填写 tweet 相关信息:
>
> ```
> <html> 
>  <head> 
>   <title>Twitter Application</title> 
>  </head> 
> <body> 
> <form > 
>  <div class="navbar"> 
>    <div class="navbar-inner"> 
>        <a class="brand" href="#">Tweet App Demo</a> 
>    </div> 
>   </div> 
>
>   <div id="main" class="container"> 
>    <table class="table table-striped"> 
>      Username: <input placeholder="Username" type="username">
>       </input> 
>   </div> 
>   <div> 
>     body: <textarea placeholder="Content of tweet" type="text"> 
>     </textarea> 
>   </div> 
>   <div> 
>   </div> 
>    <button type="submit">Add Tweet</button> 
>   </table> 
>
>  </form> 
>   <script src="http://cdnjs.cloudflare.com/ajax/libs/
>    jquery/1.8.3/jquery.min.js"></script> 
>   <script src="http://cdnjs.cloudflare.com/ajax/libs/
>     knockout/2.2.0/knockout-min.js"></script> 
>    <link href="http://netdna.bootstrapcdn.com/twitter-
>      bootstrap/2.3.2/css/bootstrap-combined.min.css" 
>     rel="stylesheet"> 
>   <!-- <script src="http://ajax.aspnetcdn.com/ajax/jquery/jquery-
>     1.9.0.js"></script> --> 
>   <script src="http://netdna.bootstrapcdn.com/twitter-
>     bootstrap/2.3.2/js/bootstrap.min.js"></script> 
>  </body> 
> </html> 
> ```

#### 在 templates/addtweets.html 中使用 observable 和 AJAX

> 新建 static/tweet.js，并向其中加入代码:
>
> ```
> function Tweet(data) { 
>   this.id = ko.observable(data.id); 
>   this.username = ko.observable(data.tweetedby); 
>   this.body = ko.observable(data.body); 
>   this.timestamp = ko.observable(data.timestamp); 
> } 
> function TweetListViewModel() { 
>   var self = this; 
>   self.tweets_list = ko.observableArray([]); 
>   self.username= ko.observable(); 
>   self.body= ko.observable(); 
>   self.addTweet = function() { 
>   self.save(); 
>   self.username(""); 
>   self.body(""); 
>    }; 
>   $.getJSON('/api/v2/tweets', function(tweetModels) { 
>   var t = $.map(tweetModels.tweets_list, function(item) { 
>     return new Tweet(item); 
>   }); 
>   self.tweets_list(t); 
>   }); 
>  self.save = function() { 
>   return $.ajax({ 
>   url: '/api/v2/tweets', 
>   contentType: 'application/json', 
>   type: 'POST', 
>   data: JSON.stringify({ 
>      'username': self.username(), 
>      'body': self.body(), 
>   }), 
>   success: function(data) { 
>      alert("success") 
>           console.log("Pushing to users array"); 
>           self.push(new Tweet({ username: data.username,body: 
>           data.body})); 
>           return; 
>   }, 
>   error: function() { 
>      return console.log("Failed"); 
>   } 
>  }); 
>   }; 
> } 
> ko.applyBindings(new TweetListViewModel()); 
> ```

#### 绑定数据到 templates/addtweets.html

> 在 templates/addtweets.html 中的 script 标签中加入tweet.js 源，并在 form 标签及其字段中加入数据绑定关系，代码如下:
>
> ```
> <html>
>   <head>
>     <title>Twitter Application</title>
>   </head>
>   <body>
>     <form data-bind="submit: addTweet">
>     <div class="navbar">
>         <div class="navbar-inner">
>             <a class="brand" href="#">Tweet App Demo</a>
>         </div>
>     </div>
>     <div id="main" class="container">
>
>       <table class="table table-striped">
>         Username: <input data-bind="value: username" placeholder="Username" type="username"></input>
>             </div>
>             <div>
>         body: <textarea data-bind="value: body" placeholder="Content of tweet" type="text"></textarea>
>             </div>
>             <div>
>             </div>
>             <button type="submit">Add Tweet</button>
>           </table>
> </form>
>     <ul data-bind="foreach: tweets_list, visible: tweets_list().length > 0">
>       <li>
>     <p data-bind="text: username"></p>
>     <p data-bind="text: body"></p>
>   <p data-bind="text: timestamp"></p>
>
>       </li>
>     </ul>
>     <script src="http://cdnjs.cloudflare.com/ajax/libs/jquery/1.8.3/jquery.min.js"></script>
>     <script src="http://cdnjs.cloudflare.com/ajax/libs/knockout/2.2.0/knockout-min.js"></script>
>     <link href="http://netdna.bootstrapcdn.com/twitter-bootstrap/2.3.2/css/bootstrap-combined.min.css" rel="stylesheet">
>     <!-- <script src="http://ajax.aspnetcdn.com/ajax/jquery/jquery-1.9.0.js"></script> -->
>     <script src="http://netdna.bootstrapcdn.com/twitter-bootstrap/2.3.2/js/bootstrap.min.js"></script>
>     <script src="{{ url_for('static', filename='tweet.js') }}"></script>
>   </body>
> </html>
> ```



