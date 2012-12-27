---
layout: post
title: Linux上配置Sphinx服务
category: Tech
tags: [Sphinx, Linux]
---


一.下载安装Sphinx:

	wget http://sphinxsearch.com/files/sphinx-0.9.9.tar.gz
	tar xzvf sphinx-0.9.9.tar.gz
	cd sphinx-0.9.9
	./configure --prefix=/usr/local/sphinx
	sudo make && make install
	
二.启动sphinx的searchd服务:

(1).配置sphinx.conf

    cd /usr/local/sphinx/etc
    cp sphinx.conf.dist sphinx.conf
	   
(2).导入自带的测试数据

    mysql -u test < /usr/local/sphinx/etc/example.sql    
	
(3).给数据做索引

    /usr/local/sphinx/bin/indexer
   
(4).启动searchd服务
  
    /usr/local/sphinx/bin/searchd
  
(5).测试全文索引

    /usr/local/sphinx/bin/search test
	


参考:

* [http://sphinxsearch.com/docs/manual-0.9.9.html#installation](http://sphinxsearch.com/docs/manual-0.9.9.html#installation)
* [http://www.viget.com/extend/installing-sphinx-on-os-x-leopard](http://www.viget.com/extend/installing-sphinx-on-os-x-leopard)
* [http://freelancing-god.github.com/ts/en/installing_sphinx.html](http://freelancing-god.github.com/ts/en/installing_sphinx.html)
