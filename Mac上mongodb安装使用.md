---
title: Mac上mongoDB的安装使用
date: 2016-07-28 11:57:11
tag: [NoSQL, mongoDB]
---

# 安装

## 更新Homebrew
首先得安装了Homebrew，这里不写了。然后更新下Homebrew的包数据库

	brew update
	
<!--more-->

## 安装支持TLS/SSL的mongoDB
	
	brew install mongodb --with-openssl

## 启动服务
To have launchd start mongodb now and restart at login

	brew services start mongodb

# 运行mongoDB
## 创建数据目录

	sudo mkdir -p /data/db

## 确定有读写权限
	chmod 777 db
## 运行
	mongod

按`Control+C`退出

	

# 进入mongo shell
>The mongo shell is an interactive JavaScript interface to MongoDB. You can use the mongo shell to query and update data as well as perform administrative operations.
>
>mongo的shell是给JavaScript连接MongoDB的接口，你可以用它来查询或者更新数据。
	
	mongo

然后显示
	
	MongoDB shell version: 3.2.8
	connecting to: test
	Welcome to the MongoDB shell.
	For interactive help, type "help".
	For more comprehensive documentation, see
	http://docs.mongodb.org/
	Questions? Try the support group
	http://groups.google.com/group/mongodb-user
	>
说明就进入了mongo的shell了

输入`quit()`退出

## 展示现在正在使用的数据库

	db
应该会返回`test`，这是默认的数据库

## 更改数据库

	use <database>
	
如果数据库不存在，执行这个命令则创建数据库

## 删除数据库
先进入该数据库，再删除

	use <database>
	db.dropDatabase()

差不多就这样了，想看具体的还是得查官方文档。

[https://docs.mongodb.com/manual/introduction/]()


	

	