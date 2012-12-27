---
layout: post
title: Ubuntu 服务器上搭建MongoDB
category: Programming
tags: [MongoDB, Ubuntu]
---


一.环境: Ubuntu Server 10.4

二.详细安装步骤：

1.创建数据库目录和log目录

	sudo adduser mongodb
	sudo mkdir /var/lib/mongodb
	sudo mkdir /var/log/mongodb
	sudo chown mongodb /var/lib/mongodb/


2.安装相关的dependencies

	sudo apt-get install curl tcsh scons g++ xulrunner-1.9-dev libpcre++-dev libboost-dev libmozjs-dev


3.安装MongoDB需要的JS库

	curl -O ftp://ftp.mozilla.org/pub/mozilla.org/js/js-1.7.0.tar.gz
	tar zxvf js-1.7.0.tar.gz
	cd js/src
	export CFLAGS=&quot;-DJS_C_STRINGS_ARE_UTF8&quot;
	make -f Makefile.ref
	sudo JS_DIST=/usr make -f Makefile.ref export


4.安装MongoDB 1.6.0 (Recommended for Production)

	git clone git://github.com/mongodb/mongo.git
	cd mongo
	git checkout -b build r1.6.0
	scons all
	sudo scons --prefix=/usr/local install

5.创建启动脚本

	sudo vim /etc/init.d/MongoDB
	# the script is here: http://gist.github.com/291349
	sudo chmod +x /etc/init.d/MongoDB
	sudo update-rc.d MongoDB defaults


6.运行Mongodb

	sudo /etc/init.d/MongoDB start


三. 使用
1.原生的启动服务方式：

	sudo mongod --dbpath /var/lib/mongodb/ --logpath /var/log/mongodb/mongod.log --fork  --logappend  --port 12345

2.停止服务：
官方强调尽量避免 sudo kill -9 ，不要uncleanly shutdown，优雅方式：

	$ ./mongo
	> db.shutdownServer()

