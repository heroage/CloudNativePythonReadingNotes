# 第四章 与数据服务交互

> 本章主要包含如下内容:
>
> * 安装 MongoDB
> * 在应用陈绪中集成 MongoDB

## Mongo 的优势和使用原因

> **MongoDB 相对于 RDBMS 的优势**
>
> * **灵活的模式\(schema\)**: Mongo 是文档数据库，一个集合中包含多个文档。在向数据库插入数据前，不需要预先定义文档模式\(schema\)，MongoDB 会根据插入的文档\(数据\)自动定义文档模式。而在 RDBMS 中，需要在插入数据前，定义好表结构。
> * **复杂性低**: 没有 RDBMS 中那么复杂的 join 操作。
> * **易扩展**
> * **访问速度快**
> * **动态查询**: MongoDB 支持对文档进行动态查询。
>
> **使用 MongoDB 的原因:**
>
> * 使用 JSON 格式文档存储数据，便于与其他应用集成
> * 可以在任何文件和属性上设置索引
> * 支持自动分片，不仅便于管理，而且可以提高运行速度
> * 在集群模式下，可以实现副本控制和高可用
>
> **MongoDB 的用途**:
>
> * 大数据
> * 用户数据管理
> * 内容交付和管理
>
> **MongoDB与Web应用程序集成框架图**  
> ![](/img/04.MongoDB与Web应用程序集成框架图.png)

## MongoDB 术语

> * **数据库**: 集合\(Collection\)的物理容器，而不是基于表。MongoDB 可以有多个数据库。
> * **集合**: 各种模式文档的组合。集合对文档的存储结构\(schema\)没有影响，基本等价于 RDBMS 中的表。
> * **文档**: 与 RDBMS 中的元组/行类似，是一组键值对。文档结构\(schema\)是动态的，在单个集合中的文档的结构可能不同，就是说同一集合中的文档字段可以不一致。
>
> 以下代码可以加深概念理解:
>
> ```
> {
>    _id : ObjectId(58ccdd1a19b08311417b14ee),  
>    body : 'New blog post,Launch your app with the AWS Startup Kit! #AWS', 
>    timestamp : "2017-03-11T06:39:40Z", 
>    id : 18, 
>    tweetedby : "eric.strom"
> }
> ```
>
> MongoDB 以二进制编码格式存储 JSON 文档，称为 BSON。

## 安装 MongoDB

> 在 Ubuntu 下安装 MongoDB 的步骤\(原书中的步骤比较复杂，经测试，在 Ubuntu18.04 下只用一行命令即可搞定\):
>
> ```
> $ sudo apt install -y mongodb
> ```
>
> 安装完成后，MongoDB 会使用 27017 端口。通过上面的命令，仅仅是安装了一个 MongoDB 的实例而已，如果要创建 MongoDB 群集，则可参考下面的链接:
>
> [ttps://docs.mongodb.com/manual/tutorial/deploy-shard-cluster/](ttps://docs.mongodb.com/manual/tutorial/deploy-shard-cluster/)

## 初始化 MongoDB 数据库

> 在前面的章节中，使用了 Sqlite3 数据库。在使用之前，我们使用 Sql 创建了需要的数据库和表。由于 MongoDB 是文档数据库，因此不必手工创建相关数据库结构，直接向其中添加新文档就好，MongoDB 会自动创建集合。
>
> 再继续之前，需要先安装 MongoDB 的数据库驱动 pymongo:
>
> ```
> $ pip install pymongo
> ```
>
> 修改 app.py 如下:
>
> ```
> # Importing modules
> from flask import Flask, render_template, request, jsonify, redirect, session
> from flask import abort
> from flask_cors import CORS, cross_origin
> from flask import make_response, url_for
> import json
> import random
> from pymongo import MongoClient
> from time import gmtime, strftime
> import sqlite3
>
> # connection to MongoDB Database
> connection = MongoClient("mongodb://localhost:27017/")
>
> # Object creation
> app = Flask(__name__)
> app.config.from_object(__name__)
> app.secret_key = '12345678'
> CORS(app)
>
> # Initialize Database
> def create_mongodatabase():
>     try:
>         dbnames = connection.list_database_names()
>         if 'cloud_native' not in dbnames:
>             db = connection.cloud_native.users
>             db_tweets = connection.cloud_native.tweets
>             db_api = connection.cloud_native.apirelease
>
>             db.insert({
>             "email": "eric.strom@google.com",
>             "id": 33,
>             "name": "Eric stromberg",
>             "password": "eric@123",
>             "username": "eric.strom"
>             })
>
>             db_tweets.insert({
>             "body": "New blog post,Launch your app with the AWS Startup Kit! #AWS",
>             "id": 18,
>             "timestamp": "2017-03-11T06:39:40Z",
>             "tweetedby": "eric.strom"
>             })
>
>             db_api.insert( {
>               "buildtime": "2017-01-01 10:00:00",
>               "links": "/api/v1/users",
>               "methods": "get, post, put, delete",
>               "version": "v1"
>             })
>             db_api.insert( {
>               "buildtime": "2017-02-11 10:00:00",
>               "links": "api/v2/tweets",
>               "methods": "get, post",
>               "version": "2017-01-10 10:00:00"
>             })
>             print ("Database Initialize completed!")
>         else:
>             print ("Database already Initialized!")
>     except:
>         print ("Database creation failed!!")
>
>
> # List users
> def list_users():
>     api_list=[]
>     db = connection.cloud_native.users
>     for row in db.find():
>         api_list.append(str(row))
>     # print (api_list)
>     return jsonify({'user_list': api_list})
>
> # List specific users
> def list_user(user_id):
>     print (user_id)
>     api_list=[]
>     db = connection.cloud_native.users
>     for i in db.find({'id':user_id}):
>         api_list.append(str(i))
>
>     if api_list == []:
>         abort(404)
>     return jsonify({'user_details':api_list})
>
> # List specific tweet
> def list_tweet(user_id):
>     print (user_id)
>     db = connection.cloud_native.tweets
>     api_list=[]
>     tweet = db.find({'id':user_id})
>     for i in tweet:
>         api_list.append(str(i))
>     if api_list == []:
>         abort(404)
>     return jsonify({'tweet': api_list})
>
> # Adding user
> def add_user(new_user):
>     api_list=[]
>     print (new_user)
>     db = connection.cloud_native.users
>     user = db.find({'$or':[{"username":new_user['username']} ,{"email":new_user['email']}]})
>     for i in user:
>         print (str(i))
>         api_list.append(str(i))
>
>     # print (api_list)
>     if api_list == []:
>     #    print(new_user)
>        db.insert(new_user)
>        return "Success"
>     else :
>        abort(409)
>
> # Deleting User
> def del_user(del_user):
>     db = connection.cloud_native.users
>     api_list=[]
>     for i in db.find({'username':del_user}):
>         api_list.append(str(i))
>
>     if api_list == []:
>         abort(404)
>     else:
>        db.remove({"username":del_user})
>        return "Success"
>
> # List tweets
> def list_tweets():
>     api_list=[]
>     db = connection.cloud_native.tweets
>     for row in db.find():
>         api_list.append(str(row))
>     # print (api_list)
>     return jsonify({'tweets_list': api_list})
>
>
> # Adding tweets
> def add_tweet(new_tweet):
>     api_list=[]
>     print (new_tweet)
>     db_user = connection.cloud_native.users
>     db_tweet = connection.cloud_native.tweets
>
>     user = db_user.find({"username":new_tweet['tweetedby']})
>     for i in user:
>         api_list.append(str(i))
>     if api_list == []:
>        abort(404)
>     else:
>         db_tweet.insert(new_tweet)
>         return "Success"
>
> def upd_user(user):
>     api_list=[]
>     print (user)
>     db_user = connection.cloud_native.users
>     users = db_user.find_one({"id":user['id']})
>     for i in users:
>         api_list.append(str(i))
>     if api_list == []:
>        abort(409)
>     else:
>         db_user.update({'id':user['id']},{'$set': user}, upsert=False )
>         return "Success"
>
> # API Routes
> @app.route('/')
> def main():
>     return render_template('main.html')
>
>
> @app.route('/addname')
> def addname():
>   if request.args.get('yourname'):
>     session['name'] = request.args.get('yourname')
>     # Redirect to main
>     return redirect(url_for('main'))
>   else:
>     # getting addname
>     return render_template('addname.html', session=session)
>
> @app.route('/clear')
> def clearsession():
>     # Clear the session
>     session.clear()
>     # Redirect the user to the main page
>     return redirect(url_for('main'))
>
> @app.route('/adduser')
> def adduser():
>     return render_template('adduser.html')
>
> @app.route('/addtweets')
> def addtweetjs():
>     return render_template('addtweets.html')
>
> @app.route("/api/v1/info")
> def home_index():
>     api_list=[]
>     db = connection.cloud_native.apirelease
>     for row in db.find():
>         api_list.append(str(row))
>     return jsonify({'api_version': api_list}), 200
>
>
>
> @app.route('/api/v1/users', methods=['GET'])
> def get_users():
>     return list_users()
>
> @app.route('/api/v1/users/<int:user_id>', methods=['GET'])
> def get_user(user_id):
>     return list_user(user_id)
>
>
> @app.route('/api/v1/users', methods=['POST'])
> def create_user():
>     if not request.json or not 'username' in request.json or not 'email' in request.json or not 'password' in request.json:
>         abort(400)
>     user = {
>         'username': request.json['username'],
>         'email': request.json['email'],
>         'name': request.json.get('name',""),
>         'password': request.json['password'],
>         'id': random.randint(1,1000)
>     }
>     return jsonify({'status': add_user(user)}), 201
>
> @app.route('/api/v1/users', methods=['DELETE'])
> def delete_user():
>     if not request.json or not 'username' in request.json:
>         abort(400)
>     user=request.json['username']
>     return jsonify({'status': del_user(user)}), 200
>
>
> @app.route('/api/v1/users/<int:user_id>', methods=['PUT'])
> def update_user(user_id):
>     user = {}
>     user['id']=user_id
>     key_list = request.json.keys()
>     for i in key_list:
>         user[i] = request.json[i]
>     return jsonify({'status': upd_user(user)}), 200
>
> @app.route('/api/v2/tweets', methods=['GET'])
> def get_tweets():
>     return list_tweets()
>
> @app.route('/api/v2/tweets', methods=['POST'])
> def add_tweets():
>     user_tweet = {}
>     if not request.json or not 'username' in request.json or not 'body' in request.json:
>         abort(400)
>     user_tweet['tweetedby'] = request.json['username']
>     user_tweet['body'] = request.json['body']
>     user_tweet['timestamp']=strftime("%Y-%m-%dT%H:%M:%SZ", gmtime())
>     user_tweet['id'] = random.randint(1,1000)
>
>     return  jsonify({'status': add_tweet(user_tweet)}), 201
>
> @app.route('/api/v2/tweets/<int:id>', methods=['GET'])
> def get_tweet(id):
>     return list_tweet(id)
>
> # Error handling
> @app.errorhandler(404)
> def resource_not_found(error):
>     return make_response(jsonify({'error': 'Resource not found!'}), 404)
>
> @app.errorhandler(409)
> def user_found(error):
>     return make_response(jsonify({'error': 'Conflict! Record exist'}), 409)
>
> @app.errorhandler(400)
> def invalid_request(error):
>     return make_response(jsonify({'error': 'Bad Request'}), 400)
>
> # Main Function
> if __name__ == '__main__':
>     create_mongodatabase()
>     app.run(host='0.0.0.0', port=5000, debug=True)
> ```
>
> 到这里，微服务的构建基本完毕，但在代码执行过程中发现，/addtweets、/adduser 返回的浏览器页面中，列表为空。这一问题，不知为何，即使全部使用了 [https://github.com/PacktPublishing/Cloud-Native-Python](https://github.com/PacktPublishing/Cloud-Native-Python) 的源代码，仍不能解决。现在就不在此处纠结了，回头再说吧。



