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
>
> 至此，在继续构建 RESTful API 之前，我们先明确待构建 RESTful API 的根 URL 以及各 URI 要实现的功能:
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
> | GET | \[[http://localhost:5000/api/v1/users/\[user\_id\]\(http://localhost:5000/api/v1/users/\[user\_id\)\](http://localhost:5000/api/v1/users/[user_id]%28http://localhost:5000/api/v1/users/[user_id%29\)\] | 根据 user\_id 返回用户详细信息 |
> | POST | [http://localhost:5000/api/v1/users](http://localhost:5000/api/v1/users) | 根据传入对象值，在后台创建新用户 |
> | DELETE | [http://localhost:5000/api/v1/users](http://localhost:5000/api/v1/users) | 根据传入的 JSON 格式文本中指定的 username 删除用户 |
> | PUT | \[[http://localhost:5000/api/v1/users/\[user\_id\]\(http://localhost:5000/api/v1/users/\[user\_id\)](http://localhost:5000/api/v1/users/[user_id]%28http://localhost:5000/api/v1/users/[user_id%29\)\] | 基于 API 调用传入的 JSON 对象中的信息，更新指定 user\_id 的信息。 |



