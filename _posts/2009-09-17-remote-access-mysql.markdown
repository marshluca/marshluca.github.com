---
layout: post
title: 远程访问MySQL
category: Ruby
tags: [Ubuntu, MySQL]
---

Ubuntu Server 下开启远程连接 MySQL，可以通过以下方式：

1.创建一个可以远程连接的 MySQL 用户

	mysql> GRANT ALL PRIVILEGES ON remote.* TO remote@"%" IDENTIFIED BY "remotepwd";

2.修改 mysql 的配置文件 /etc/mysql/my.cnf

    # Instead of skip-networking the default is now to listen only on
    # localhost which is more compatible and is not less secure.	
    # bind-address           = 127.0.0.1

将bind-address一行注释掉或者把允许访问的 ip 填上

    bind-address         = 192.168.1.100

3.然后重启 MySQL

	$> sudo /etc/init.d/mysql restart
	* Starting MySQL database server mysqld
	...done.
	* Checking for corrupt, not cleanly closed and upgrade needing tables.
	andy@ubuntu:~$ sudo /etc/init.d/mysql start
	* Starting MySQL database server mysqld
	...done.

4.远程访问 MySQL 
  
	mysql -u remote -h REMOTE_SERVER_ADDRESS -p