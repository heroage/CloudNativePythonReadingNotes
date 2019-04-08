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
> 我在 github 上创建了账号，并创建代码库: https://github.com/heroage/Cloud-Native-Python.git
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



