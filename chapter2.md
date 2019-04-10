# 第2章 使用 Python 构建微服务

> 本章主要内容包括:
>
> * 构建 REST API
> * 测试构建的 API

## Python 概念解析

> 略……

## 微服务模型

> 微服务架构和应用程序的工作流程
>
> ![](/img/02.微服务架构和应用程序的工作流程.png)

## 构建微服务

> 本书使用 Flask 作为 Web 框架。
>
> 我在 github 上创建了账号，并创建代码库: [https://github.com/heroage/Cloud-Native-Python.git](https://github.com/heroage/Cloud-Native-Python.git)
>
> 我们使用以下命令在本地创建代码库，并 push 到 github 代码库:
>
> ```
> $ mkdir Cloud-Native-Python  # Creating the directory
> $ cd Cloud-Native-Python  # Changing the path to working directory
> $ git init . # Initialising the local directory
> $ echo "Cloud-Native-Python" > README.md  # Adding description of repository
> $ git add README.md  # Adding README.md
> $ git commit -am "Initial commit"  # Committing the changes
> $ git remote add origin https://github.com/heroage/Cloud-Native-Python.git  # Adding to local repository
> $ git push -u origin master  # Pushing changes to remote repository.
> ```
>
> 我们将沿用这种方式构建应用程序，直到完成微服务的构建。
>
> 先安装基于文件的数据库 SQLite V3，用作微服务的数据存储。
>
> 在 Ubuntu 下，使用如下命令:
>
> ```
> $ sudo apt install sqlite3 libsqlite3-dev -y
> ```
>
> 创建和使用\(source\) virtualenv 环境，将本地应用程序的环境和全局 site-package 安装隔离开来。可以使用如下命令安装 virtualenv:
>
> ```
> $ pip install virtualenv
> ```
>
> 使用如下命令创建 virtualenv 环境:
>
> ```
> $ virtualenv env --no-site-packages --python=python3
> $ source env/bin/activate
> ```
>
> 完成 virtualenv 的安装后，还需要为虚拟环境安装一个依赖软件，即 Flask\(0.10.1\)。使用以下命令:
>
> ```
> $ echo "Flask==0.10.1" >>  requirements.txt
> $ pip install -r requirements.txt
> ```
>
> 依赖性安装完成后，传建一个 app.py 文件，包含以下内容:
>
> ```py
> from flask import Flask
> app = Flask(__name__)
> if __name__ == "__main__":
>     app.run(host='0.0.0.0', port=5000, debug=True)
> ```
>
> 尝试运行程序，查看程序是否工作正常，执行下面的命令:
>
> ```
> $ python app.py
> ```

### 创建 V1 版 RESTful API

> 至此，我们已经完成了构建 RESTful API 的准备，在继续之前我们先明确一下 V1 版本 RESTful API \(用户相关 API\)的根 URL，并对 API 要实现的功能进行规划，定义各 URI 要实现的功能:
>
> **根 URL: **
>
> ```
> http://localhost:5000/api/v1
> ```
>
> **URI功能说明:**
>
> | **HTTP Method** | **URI** | **动作** |
> | :---: | :--- | :--- |
> | GET | [http://localhost:5000/api/v1/info](http://localhost:5000/api/v1/info) | 返回版本 |
> | GET | [http://localhost:5000/api/v1/users](http://localhost:5000/api/v1/users) | 返回用户列表 |
> | GET | [http://localhost:5000/api/v1/users/&lt;user\_id&gt;](http://localhost:5000/api/v1/users/<user_id&gt) | 根据 user\_id 返回用户详细信息 |
> | POST | [http://localhost:5000/api/v1/users](http://localhost:5000/api/v1/users) | 根据传入对象值，在后台创建新用户 |
> | DELETE | [http://localhost:5000/api/v1/users](http://localhost:5000/api/v1/users) | 根据传入的 JSON 格式文本中指定的 username 删除用户 |
> | PUT | [http://localhost:5000/api/v1/users/&lt;user\_id&gt;](http://localhost:5000/api/v1/users/<user_id&gt) | 基于 API 调用传入的 JSON 对象中的信息，更新指定 user\_id 的信息。 |

#### 创建第一个 API: /api/v1/info

> 首先在 SQLite3 中创建一个 apirelease 的表结构，其中包含 API 版本和发布信息。运行下列命令:
>
> ```
> $ sqlite3
> sqlite> CREATE TABLE apirelease(
>     buildtime date,
>     version varchar(30) primary key,
>     links varchar2(30), methods varchar2(30));
>
> sqlite> Insert into apirelease values ('2017-01-01 10:00:00', "v1", "/api/v1/users", "get, post, put, delete");
>
> sqlite> .save /mydir/mydb.db
> ```
>
> 然后，在 app.py 中定义函数和路由 /api/vi/info，用于处理对 /api/v1/info 路径的 RESTful 调用。
>
> ```
> # app.py
>
> from flask import Flask, jsonify
> import json
> import sqlite3
>
> app = Flask(__name__)
>
> @app.route('/api/v1/info')
> def home_index():
>         conn = sqlite3.connect('/mydir/mydb.db')
>         print('Opened database successfully')###
>         api_list = []
>         cursor = conn.execute('SELECT buildtime, version, methods, links from apirelease')
>         for row in cursor:
>                 api = {}
>                 api['version'] = row[0]
>                 api['buildtime'] = row[1]
>                 api['methods'] = row[2]
>                 api['links'] = row[3]
>                 api_list.append(api)
>         conn.close()
>         return jsonify({'api_version': api_list}), 200
>
> if __name__ == "__main__":
>         app.run(host='0.0.0.0', port=5000, debug=True)
> ```
>
> 完成以上工作，就可以使用浏览器或 telnet 终端访问 [http://localhost:5000/api/v1/info，执行](http://localhost:5000/api/v1/info，执行) RESTful 调用了。

#### 构建 users 表

> 为 user 定义如下字段:
>
> * id
> * username
> * email
> * password
> * full\_name
>
> 使用下列命令在 SQLite 中创建 users 表结构:
>
> ```
> sqlite> .open /mydir/mydb.db
>
> sqlite> CREATE TABLE users(
>     username varchar2(30),
>     email varchar2(30),
>     password varchar2(30),
>     full_name varchar(30),
>     id integer primary key autoincrement);
> ```

#### GET /api/v1/users

> 在 app.py 中增加如下代码:
>
> ```
> @app.route('/api/v1/users', methods=['GET'])
> def get_users():
>         return list_users()
>
> def list_users():
>         conn = sqlite3.connect('/mydir/mydb.db')
>         print('Opened database successfully')
>         api_list = []
>         cursor = conn.execute('SELECT username, full_name, email, password, id from users')
>         for row in cursor:
>                 a_dict = {}
>                 a_dict['username'] = row[0]
>                 a_dict['name'] = row[1]
>                 a_dict['email'] = row[2]
>                 a_dict['password'] = row[3]
>                 a_dict['id'] = row[4]
>                 api_list.append(a_dict)
>         conn.close()
>         return jsonify({'user_list': api_list})
> ```

#### GET /api/v1/users/\[user\_id\]

> 在 app.py 中增加如下代码:
>
> ```
> from flask import abort
>
> @app.route('/api/v1/users/<int:user_id>', methods=['GET'])
> def get_user(user_id):
>         return list_user(user_id)
>
> def list_user(user_id):
>         conn = sqlite3.connect('/mydir/mydb.db')
>         print('Opened database successfully')
>         cursor = conn.execute('SELECT username, full_name, email, password, id from users where id=?', (user_id,))
>         data = cursor.fetchall()
>         if len(data) == 0:
>              abort(404)
>         else:        
>              user = {}
>              user['username'] = data[0][0]
>              user['name'] = data[0][1]
>              user['email'] = data[0][2]
>              user['password'] = data[0][3]
>              user['id'] = data[0][4]
>         conn.close()
>         return jsonify(user), 200
> ```
>
> 这样，如果请求的 user\_id 不存在，Flask 应用会返回 404 错误。由于我们开发的 Web 应用，因此需要为 API 球球返回 JSON，而非 HTML。因此需要对错误处理稍加修改:
>
> ```
> from flask import make_response
>
> @app.errorhandler(404)
> def resource_not_found(error):
>     return make_reponse(jsonify({'error': 'Resource not found!'}), 404)
> ```

#### POST /api/v1/users

> 通过 POST 方法向列表中添加新用户，有两种传递数据的方法:
>
> * **JSON**:  将 JSON 记录\(即将要添加的记录转换为 JSON 形式\)作为一个对象，传递给 request，作为请求的一部分。RESTful  API 调用格式大致如下:
>   curl -i -H "Content-Type: application/json" -X POST -d {"field1":"value"} resourcce\_url
> * **参数形式**: 将要添加的记录值作为 URL 的参数传递给 request
>
> 本书采用第一种方式，即 JSON 方式，在 app.py 中增加如下代码:
>
> ```
> @app.route('/api/v1/users', methods=['POST'])
> def create_user():
>         if not request.json or not 'username' in request.json or not 'email' in request.json or not 'password' in request.json:
>                 abort(400)
>         user = {
>                 'username': request.json['username'],
>                 'email': request.json['email'],
>                 'name': request.json.get('name', ''),
>                 'password': request.json['password']
>         }
>         return jsonify({'status': add_user(user)}), 201
>
> def add_user(new_user):
>         conn = sqlite3.connect('/mydir/mydb.db')
>         print('Opened database successfully')
>         api_list = []
>         cursor = conn.cursor()
>         cursor.execute('SELECT * from users where username=? or email=?', (new_user['username'], new_user['email']))
>         data = cursor.fetchall()
>         if len(data) != 0:
>                 conn.close()
>                 abort(409)
>         else:
>                 cursor.execute('insert into users (username, email, password, full_name) values(?,?,?,?)', (new_user['username'], new_user['email'],
>                         new_user['password'], new_user['name']))
>                 conn.commit()
>                 conn.close()
>                 return 'Success'
> ```
>
> 添加以上代码后，使用 API 调用测试:
>
> ```
> $ curl -i -H "Content-Type: application/json" -X POST -d '{"username":"mahesh2@rocks", "email": "mahesh99@gmail.com","password": "mahesh123", "name":"Mahesh" }' http://localhost:5000/api/v1/users
> ```
>
> 第一次调用，通常会显示 Success。但第二次之后，就会提示 409 冲突错误，原因是意图增加重复记录。

#### DELETE /api/v1/users

> delete 方法用于删除指定 username 的用户。需要传递给 request 一个包含 username 的 JSON 对象。向 app.py 中加入以下代码:
>
> ```
> @app.route('/api/v1/users', methods=['DELETE'])
> def delete_user():
>         if not request.json or not 'username' in request.json:
>                 abort(400)
>         user = request.json['username']
>         return jsonify({'status': del_user(user)}), 200
>
> def del_user(del_user):
>         conn = sqlite3.connect('/mydir/mydb.db')
>         print('Opened database successfully')
>         cursor = conn.cursor()
>         cursor.execute('SELECT * from users where username=?', (del_user,))
>         data = cursor.fetchall()
>         print('Data', data)
>         if len(data) == 0:
>                 conn.close()
>                 abort(404)
>         else:
>                 cursor.execute('delete from users where username=?', (del_user,))
>                 conn.commit()
>                 conn.close()
>                 return 'Success'
> ```
>
> 添加以上代码后，使用 API 调用测试:
>
> ```
> $ curl -i -H "Content-Type: application/json" -X delete -d '{"username":"manish123" }' http://localhost:5000/api/v1/users
> ```

#### PUT /api/v1/users

> PUT API 用来更新指定用户的信息，向 app.py 中加入如下代码:
>
> ```
> @app.route('/api/v1/users/<int:user_id>', methods=['PUT'])
> def update_user(user_id):
>         user = {}
>         if not request.json:
>                 abort(400)
>         user['id'] = user_id
>         key_list = request.json.keys()
>         for i in key_list:
>                 user[i] = request.json[i]
>         print(user)
>         return jsonify({'status': upd_user(user)}), 200
>
> def upd_user(user):
>         conn = sqlite3.connect('/mydir/mydb.db')
>         print('Opened database successfully')
>         cursor = conn.execute('SELECT * from users where id=?', (user['id'],))
>         data = cursor.fetchall()
>         print(data)
>         if len(data) == 0:
>                 abort(404)
>         else:
>                 key_list = user.keys()
>                 for i in key_list:
>                         if i != 'id':
>                                 cursor.execute('UPDATE users SET {0}=? WHERE id=?'.format(i), (user[i], user['id']))
>                                 conn.commit()
>                 conn.close()
>                 return 'Success'
> ```
>
> 添加以上代码后，使用 API 调用测试:
>
> ```
> $ curl -i -H "Content-Type: application/json" -X put -d '{"password":"mahesh@rocks" }' http://localhost:5000/api/v1/users/4
> ```

### 构建 V2 版 RESTful API

> 前面定义了 V1 版的 API，下面将定义 V2 版的 API，向微服务中添加 tweet 资源。首先，向 Sqlite3 插入 V2 信息:
>
> ```
> $ sqlite3
> sqlite> .open /mydir/mydb.db
> sqlite> Insert into apirelease values ('2017-01-11 12:20:00', "v2", "/api/v2/tweets", "get, post");
> ```
>
> 添加数据库信息后，使用 API 调用测试:
>
> ```
> $ curl http://localhost:5000/api/v1/info
> ```
>
> 下面对 V2 API 的 URI 进行规划:
>
> > | **HTTP Method** | **URI** | **Actions** |
> > | :--- | :--- | :--- |
> > | GET | [http://localhost:5000/api/v2/tweets](http://localhost:5000/api/v2/tweets) | 获取 tweet 列表 |
> > | GET | [http://localhost:5000/api/v2/tweets/&lt;id&gt;](http://localhost:5000/api/v2/users/<user_id\&gt) | 获取指定 id 的 tweet |
> > | POST | [http://localhost:5000/api/v2/tweets](http://localhost:5000/api/v2/tweets) | 通过传给 API 的 JSON 数据新建 tweet\(一个或多个\)，并保存到数据库 |
>
> tweet 包含以下字段:
>
> * id
> * username
> * body
> * tweet\_time
>
> 在 Sqlite3 中定义 tweets 表:
>
> ```
> $ sqlite3
> sqlite> .open /mydir/mydb.db
> sqlite> CREATE TABLE tweets( 
>     id integer primary key autoincrement, 
>     username varchar2(30), 
>     body varchar2(30), 
>     tweet_time date);
> ```

#### GET /api/v2/tweets

> 获取用户所有 tweets，在 app.py 中增加如下代码:
>
> ```
> @app.route('/api/v2/tweets', methods=['GET'])
> def get_tweets():
>         return list_tweets()
>
> def list_tweets():
>         conn = sqlite3.connect('/mydir/mydb.db')
>         print('Opened database successfully')
>         api_list = []
>         cursor = conn.execute('SELECT username, body, tweet_time, id from tweets')
>         data = cursor.fetchall()
>         conn.close()
>         if data != 0:
>                 tweet = {}
>                 tweet['Tweet By'] = data[0][0]
>                 tweet['Body'] = data[0][1]
>                 tweet['Timestamp'] = data[0][2]
>                 tweet['id'] = data[0][3]
>                 api_list.append(tweet)
>                 return jsonify({'tweets_list': api_list})
>         else:
>                 return api_list
> ```
>
> 添加以上代码后，使用 API 调用测试，由于未添加任何 tweet，因此结果为空:
>
> ```
> $ curl http://localhost:5000/api/v2/tweets -v
> ```

#### POST /api/v2/tweets

> 使用 POST 为指定用户添加 tweet，在 app.py 中增加如下代码:
>
> ```
> from time import gmtime, strftime
>
> @app.route('/api/v2/tweets', methods=['POST'])
> def add_tweets():
>         user_tweet = {}
>         if not request.json or not 'username' in request.json or not 'body' in request.json:
>                 abort(400)
>         user_tweet['username'] = request.json['username']
>         user_tweet['body'] = request.json['body']
>         user_tweet['created_at'] = strftime('%Y-%m-%dT%H:%M:%SZ', gmtime())
>         print(user_tweet)
>         return jsonify({'status': add_tweet(user_tweet)}), 201
>
> def add_tweet(new_tweets):
>         conn = sqlite3.connect('/mydir/mydb.db')
>         print('Opened database successfully')
>         cursor = conn.execute('SELECT * from users where username=?', (new_tweets['username'],))
>         data = cursor.fetchall()
>
>         if len(data) == 0:
>                 conn.close()
>                 abort(404)
>         else:
>                 cursor.execute('INSERT into tweets (username, body, tweet_time) values (?,?,?)',
>                         (new_tweets['username'], new_tweets['body'], new_tweets['created_at']))
>                 conn.commit()
>                 conn.close()
>                 return 'Success'
> ```
>
> 添加以上代码后，使用 API 调用测试:
>
> ```
> $ curl -i -H "Content-Type: application/json" -X POST -d '{"username":"mahesh@rocks","body": "It works" }' http://localhost:5000/api/v2/tweets
> $ curl http://localhost:5000/api/v2/tweets -v
> ```

#### GET /api/v2/tweets/&lt;id&gt;

> GET 方法获取指定 ID 的 tweet，向 app.py 中加入以下代码:
>
> ```
> @app.route('/api/v2/tweets/<int:id>', methods=['GET'])
> def get_tweet(id):
>         return list_tweet(id)
>
> def list_tweet(user_id):
>         print(user_id)
>         conn = sqlite3.connect('/mydir/mydb.db')
>         print('Opened database successfully')
>         api_list = []
>         cursor = conn.cursor()
>         cursor.execute('SELECT * from tweets where id=?', (user_id,))
>         data = cursor.fetchall()
>         conn.close()
>         print(data)
>         if len(data) == 0:
>                 abort(404)
>         else:
>                 user = {}
>                 user['id'] = data[0][0]
>                 user['username'] = data[0][1]
>                 user['body'] = data[0][2]
>                 user['tweet_time'] = data[0][3]
>                 return jsonify(user)
> ```

### 测试 RESTful API

> 本节将便携可以用做系统单独运行的测试用例，以确保 RESTful API 作为后端服务在生产环境中正常运行。测试大致可分为以下几种:
>
> * **功能性测试**: 主要用于测试组件或系统的功能。可以验证组件功能规范。
> * **非功能性测试**: 针对组件质量特性的测试，包括效率测试、可靠性测试等等
> * **结构测试**: 用于测试系统结构，测试人员必须了解代码的内部实现才能编写测试用例。

#### 单元测试

> 单元测试用于测试系统中的工作单元和逻辑单元代码，其特点如下:
>
> * **自动化**: 可以自动执行
> * **独立**: 不应该有任何依赖
> * **持续和可重复**: 保持幂等性\(idempotency，在编程中一个幂等操作的特点是其任意多次执行所产生的影响均与一次执行的影响相同。\)
> * **可维护**: 容易理解和更新
>
> 本书使用 nose 单元测试框架，以下面的命令安装:
> ```
> $ pip install nose
> ```
>
> 或是把 nose 写到 requirements.txt 文件中:
>
> ```
> $ pip install -r requirements.txt
> ```
>
> > requirements.txt 文件格式如下:
> >
> > ```
> > alembic==0.8.6
> > bleach==1.4.3
> > click==6.6
> > dominate==2.2.1
> > Flask==0.11.1
> > Flask-Bootstrap==3.3.6.0
> > Flask-Login==0.3.2
> > Flask-Migrate==1.8.1
> > Flask-Moment==0.5.1
> > Flask-PageDown==0.2.1
> > Flask-Script==2.0.5
> > Flask-SQLAlchemy==2.1
> > Flask-WTF==0.12
> > ```
>
> 完成 nose 的安装后，新建一个 flash\_test.py 文件，向文件中加入以下代码:
>
> ```
> from app import app
> import unittest
>
> class FlaskappTests(unittest.TestCase):
>     def setUp(self):
>         # creates a test client
>         self.app = app.test_client()
>         # propagate the exceptions to the test client
>         self.app.testing = True
>
>     def test_users_status_code(self):
>         # sends HTTP GET request to the application
>         result = self.app.get('/api/v1/users')
>         # assert the status code of the response
>         self.assertEqual(result.status_code, 200)
>
>     # The GET /api/v2/tweets test case is given as follows:
>     def test_tweets_status_code(self):
>         # sends HTTP GET request to the application
>         result = self.app.get('/api/v2/tweets')
>         # assert the status code of the response
>         self.assertEqual(result.status_code, 200)
>
>     # The GET /api/v1/info test case is as follows:
>     def test_tweets_status_code(self):
>         # sends HTTP GET request to the application
>         result = self.app.get('/api/v1/info')
>         # assert the status code of the response
>         self.assertEqual(result.status_code, 200)
>
>     # The POST /api/v1/users test case is written like this:
>     def test_addusers_status_code(self):
>         # sends HTTP POST request to the application
>         result = self.app.post('/api/v1/users', 
>             data='{"username": "manish21", "email":"manishtest@gmail.com", "password": "test123"}',
>             content_type='application/json')
>         print (result)
>         # assert the status code of the response
>         self.assertEquals(result.status_code, 201)
>
>     # The PUT /api/v1/users test case is as follows:
>     def test_updusers_status_code(self):
>         # sends HTTP PUT request to the application
>         # on the specified path
>         result = self.app.put('/api/v1/users/4', 
>             data='{"password": "testing123"}', content_type='application/json')
>         # assert the status code of the response
>         self.assertEquals(result.status_code, 200)
>
>     # The POST /api/v1/tweets test case is as follows:
>     def test_addtweets_status_code(self):
>         # sends HTTP GET request to the application
>         # on the specified path
>         result = self.app.post('/api/v2/tweets', 
>             data='{"username": "mahesh@rocks", "body":"Wow! Is it working #testing"}',
>             content_type='application/json')
>         # assert the status code of the response
>         self.assertEqual(result.status_code, 201)
>
>     # The DELETE /api/v1/users test case is given as follows:
>     def test_delusers_status_code(self):
>         # sends HTTP Delete request to the application
>         result = self.app.delete('/api/v1/users', 
>         data='{"username": "manish21"}', content_type='application/json')
>         # assert the status code of the response
>         self.assertEquals(result.status_code, 200)
> ```
>
> 以上代码就是单元测试的范例，当然也可以根据需要编写更多的测试用例。完成测试用例的编写，执行下面的命令，即可执行所有的测试用例:
>
> ```
> $ nosetests
> ```



