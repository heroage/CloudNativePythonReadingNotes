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
> ```js
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

### CORS -- 跨源资源共享

> CORS\(Cross-Origin Resource Sharing\)有助于在 API 服务器和客户端之间保持 API 请求的数据完整性。其理念是，服务器和客户端之间应当有足够的信息进行相互认证，并使用 HTTP header 通过安全通道传输数据。
>
> 当客户端进行 API 调用时，通常是发送 GET 或 POST 请求，其中 body 通常是 text/plain，而其 header 称为 Origin\(其中包括请求页面的协议、域名和端口\)。当服务器确认请求时，向同一 Origin 发送响应，响应中会附带 Access-Control-Allow-Origin 头，这样就确保了响应会被正确的 Origin 收到。
>
> 如此这般，Origin 之间就产生了资源分享。
>
> 几乎所有的浏览器都支持 Origin，如 IE 8+、Firefox 3.5+ 和 Chrome 之类。
>
> 首先，我们用下面的命令在 Flashk 中安装 CORS 模块:
>
> ```
> $ pip install flask-cors
> ```
>
> 这个包中包含了个 Flask 扩展，它默认启用了所有 Origin 和方法的 CORS 支持。完成安装后，可以使用以下代码在 app.py 中引入这个包，并启用 app 及相关资源的 CORS 支持:
>
> ```
> from flask_cors import CORS, cross_origin
>
> CORS(app)
> cors = CORS(app, resources={r"/api/*": "origins": “*”}})
> ```
>
> 本书这里讲的太概念化，我是一头雾水，实在应该补全一点何谓 CORS 的资料，看看这段可以有些概念上的了解:
>
> HTTP访问控制\(CORS\):
>
> [https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access\_control\_CORS](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)

### Session 管理

> 会话\(Session\)是与用户相关的一系列请求和响应。会话工作在服务器端，用于主要用户认证，以及记录、跟踪用户在网页间的各种活动。每个客户端会话都被赋予一个会话 ID。会话通常存放在客户端 Cookies 的顶层，并由服务器用密钥加密。在服务器端则由 Flask 应用程序快速地使用密钥解密。
>
> > **题外:**
> >
> > 关于 Session 的话题，还需要进一步深入看看，下面是一段简单的说明。
> >
> > session 是基于 cookie 的一种会话技术。session 数据存放存放在服务器端，客户端通过 cookie 中携带的 SESSIONID 来访问服务端，以获取 SESSIONID 对应的 session 数据。
>
> 下面新建 templates/main.html，并在代码中演示会话的设置及使用:
>
> ```
> <html> 
>   <head> 
>     <title>Twitter App Demo</title> 
>     <link rel=stylesheet type=text/css href="{{ url_for('static', filename='style.css') }}" /> 
> </head> 
> <body> 
>     <div id="container"> 
>       <div class="title"> 
>         <h1></h1> 
>       </div> 
>       <div id="content"> 
>         {% if session['name'] %} 
>         Your name seems to be <strong>{{session['name']}}</strong>.
>        <br/> 
>         {% else %} 
>         Please set username by clicking it <a href="{{ url_for('addname') }}">here</a>.<br/>
>         {% endif %} 
>        Visit <a href="{{ url_for('adduser') }}">this for adding new 
>        application user </a> or <a href="{{ url_for('addtweets') }}">this to add new tweets</a> page to interact with RESTFUL API. 
>
>        <br /><br /> 
>        <strong><a href="{{ url_for('clearsession') }}">Clear session</a></strong> 
>         </div> 
>         </div> 
>     </div> 
>    </body> 
> </html>
> ```
>
> 下面修改 app.py，新增 /、/addname 、/clear路由，为应用增加入口:
>
> ```
> from flask import session
> from flask import redirect, url_for
>
> app.config['SECRET_KEY'] = '123456'
>
> @app.route('/')
> def main():
>         return render_template('main.html')
>
> @app.route('/addname')
> def addname():
>         if request.args.get('yourname'):
>                 session['name'] = request.args.get('yourname')
>                 # And ten redirect the user to the main page
>                 return redirect(url_for('main'))
>         else:
>                 return render_template('addname.html', session=session)
>
> @app.route('/clearsession')
> def clearsession():
>         # Clear the session
>         session.clear()
>         # Redirect the user to the main page
>         return redirct(url_for('main'))
> ```
>
> 新建 templates/addname.html，并加入如下代码:
>
> ```
> <html>
>   <head>
>     <title>Twitter App Demo</title>
>     <link rel=stylesheet type=text/css href="{{ url_for('static', filename='style.css') }}">
>   </head>
>  <body>
>     <div id="container">
>         <div class="title">
>             <h1>Enter your name</h1>
>         </div>
>      <div id="content">
>        <form method="get" action="{{ url_for('addname') }}">
>          <label for="yourname">Please enter your name:</label>
>          <input type="text" name="yourname" /><br />
>          <input type="submit" />
>        </form>
>      </div>
>      <div class="title">
>             <h1></h1>
>      </div>
>      <code><pre>
>      </pre></code>
>      </div>
>     </div>
>    </body>
> </html>
> ```

### Cookies

> Cookie 跟 Session 很像，不同之处在于 Cookies 是以文本文件的形式储存在客户端本地，而不是由服务器维护。
>
> Cookie 的主要目的是跟踪客户使用情况，其属性存储在 request 对象中。request 对象是一个键值对的集合，包括 cookie、变量及相关值。
>
> 可以使用 request.set\_cookie 来设置 cookie，使用 request.cookies.get 来读取 cookie:
>
> ```
> @app.route('/set_cookie') 
> def cookie_insertion(): 
>   redirect_to_main = redirect('/') 
>   response = make_response(redirect_to_main )   
>   response.set_cookie('cookie_name',value='values') 
>   cookie = request.cookies.get('my_cookie')
>   return response
> ```



