---
layout: post
title: MongoDB Repair 修复数据
category: Database
tags: [MongoDB]
---


在 Rails 3.0.5 中使用mongodb的时候，突然发现rails server启动不了，提示错误：
	
> cannot remove Object::NotSupported	

跟踪log，发现rails无法加载initializers下的mongodb配置连接, 原因是mongodb服务突然停止了。
但是在shell下却又无法启动mongod进程, 下面是报错：

>  couldn't connect to server 127.0.0.1 (anon):1154
>  exception: connect failed

比较奇怪。

在Mongodb<a href="http://www.mongodb.org/display/DOCS/Starting+and+Stopping+Mongo">官方文档</a>中有提到：

> Sending a KILL signal kill -9 will probably cause damage as mongod will not be able to cleanly exit.  (In such a scenario, run the repairDatabase command.

> After a unclean shutdown, MongoDB will say it was not shutdown cleanly, and ask you to do a repair. This is absolutely not the same as corruption, this is MongoDB saying it can't 100% verify what's going on, and to be paranoid, run a repair.


看来是进程被不小心kill掉了，属于不优雅的停止方式, 一定程度上会对数据库造成破坏, 需要进行repair修复：

	sudo mongod --repair --dbpath /usr/local/mongodb_data/


启动方式可以参考<a href='/2011/03/27/start-mongodb-service.html'>MongoDB服务的启动和停止</a>.
如果已经配置了自动启动脚本，会发现mongodb已经启动了。
