---
layout: post
title: Linux上gem install mysql经常碰到的问题
category: Tech
tags: [gem, MySQL]
---


在Linux上通过gem install mysql的时候, 经常会碰到一些问题, 整理一下遇到的及其解决办法.

1.mkmf (LoadError)

    `require’: no such file to load — mkmf (LoadError) when installing the “mysql” gem

  解决办法:
  
    sudo apt-get install ruby1.8-dev


2.依赖相关问题

	/usr/bin/ruby1.8 extconf.rb install mysql
	checking for mysql_query() in -lmysqlclient... no
	checking for main() in -lm... yes
	checking for mysql_query() in -lmysqlclient... no
	checking for main() in -lz... yes
	checking for mysql_query() in -lmysqlclient... no
	checking for main() in -lsocket... no
	checking for mysql_query() in -lmysqlclient... no
	checking for main() in -lnsl... yes
	checking for mysql_query() in -lmysqlclient... no
	*** extconf.rb failed ***
	Could not create Makefile due to some reason, probably lack of
	necessary libraries and/or headers.  Check the mkmf.log file for more
	details.  You may need configuration options.

  解决办法:
  
    apt-get install libmysql++-dev
    sudo apt-get install libmysqlclient15-dev  
    sudo gem install mysql