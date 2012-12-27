---
layout: post
title: Rails3中使用thinking_sphinx做全文检索
category: Database
tags: [Sphinx, Rails]
---

Sphinx是Rails中比较常用的一种全文检索引擎，性能方面非常出色，但是目前并不支持中文索引。
Coreseek是基于Sphinx开发的一款中文检索引擎，内置mmseg中文分词技术，下面是具体的配置和使用.

一.环境：

* Mac OS X 10.6.7
* Rails 3.0.5
* Coreseek 3.2.14
* thinking-sphinx 2.0.3

二.安装
1.设置环境，升级/安装系统基础依赖包：m4(>=1.4.13)、autoconf(>=2.65)、automake(>=1.11)、libtool(>=2.26b)

    #设置路径和中文环境：
    $ export PATH=/usr/local/bin:$PATH
    $ export LC_ALL=zh_CN.UTF-8
    $ export LANG=zh_CN.UTF-8

    $ curl -O -L http://mirrors.kernel.org/gnu/m4/m4-1.4.16.tar.gz
    $ tar -xzvf m4-1.4.16.tar.gz
    $ cd m4-1.4.16
    $ ./configure --prefix=/usr/local
    $ make && make install
    $ cd ..

    $ curl -O -L http://mirrors.kernel.org/gnu/autoconf/autoconf-2.68.tar.gz
    $ tar -xzvf autoconf-2.68.tar.gz
    $ cd autoconf-2.68
    $ ./configure --prefix=/usr/local
    $ make && make install
    $ cd ..

    $ curl -O -L http://mirrors.kernel.org/gnu/automake/automake-1.11.tar.gz
    $ tar xzvf automake-1.11.tar.gz
    $ cd automake-1.11
    $ ./configure --prefix=/usr/local
    $ make && make install
    $ cd ..

    $ curl -O -L http://mirrors.kernel.org/gnu/libtool/libtool-2.4.tar.gz
    $ tar xzvf libtool-2.4.tar.gz
    $ cd libtool-2.4
    $ ./configure --prefix=/usr/local
    $ make && make install
    $ cd ..

2.安装Coreseek

  （1）下载
  
    $ curl -O -L http://www.coreseek.cn/uploads/csft/3.2/coreseek-3.2.14.tar.gz
    $ tar xzvf coreseek-3.2.14.tar.gz
    $ cd coreseek-3.2.14

  （2）安装mmseg （中文分词支持）
  
    $ cd mmseg-3.2.14
    $ ./bootstrap
    $ ./configure --prefix=/usr/local/mmseg3
    $ make && make install
    $ cd ..

  （3）安装coreseek （搜索支持，需提前安装mysql以支持mysql数据源）
    $ cd csft-3.2.14
    $ sh buildconf.sh
    $ ./configure --prefix=/usr/local/coreseek  --without-unixodbc --with-mmseg --with-mmseg-includes=/usr/local/mmseg3/include/mmseg/ --with-mmseg-libs=/usr/local/mmseg3/lib/
    $ make && make install
    $ cd ..

  （4）测试
     在testpack/etc 下有多种测试方案，这里使用mysql方式:
     # 导入数据到mysql
     $ mysql -u test test < testpack/var/test/documents.sql
     # 修改配置文件
     $ vi etc/csft_mysql.conf
     #  修改sql_user为test，跟上面导入数据用户保持一致
     # 创建索引
     $ /usr/local/coreseek/bin/indexer -c etc/csft_mysql.conf --all
     # 检索测试
     $ /usr/local/coreseek/bin/search 百度 

     如果可以有匹配显示，就说明安装没要问题了。
     
   （5）启动： ( 默认会使用 etc/csft.conf, 可以通过-c指定配置文件 )
   
     /usr/local/coreseek/bin/searchd 

三.Rails 3.0.5中通过thinking_sphinx来实现全文检索 

1.安装thinking_sphinx

    # Gemfile中配置 
    gem 'thinking-sphinx', '2.0.3', :require => 'thinking_sphinx'
    # bundle install
   
2.sphinx配置：
   thinking_sphinx默认会查找config/sphinx.yml
   
    development:
       port: 9312
       charset_type: zh_cn.utf-8
       charset_dictpath: /usr/local/mmseg3/etc/ 
       bin_path: /usr/local/coreseek/bin
       searchd_binary_name: searchd
       indexer_binary_name: indexer  
	   
3.在model中定义了相关index后，就可以生成配置文件，创建index, 并启动服务：

	$ rake ts:conf
	$ rake ts:index
	$ rake ts:start

参考：

* [http://freelancing-god.github.com/ts/en/advanced_config.html](http://freelancing-god.github.com/ts/en/advanced_config.html)
* [http://www.coreseek.cn/products-install/coreseek_mmseg](http://www.coreseek.cn/products-install/coreseek_mmseg)
* [http://www.coreseek.cn/docs/coreseek_3.2-sphinx_0.9.9.html](http://www.coreseek.cn/docs/coreseek_3.2-sphinx_0.9.9.html)
* [http://www.blog.bridgeutopiaweb.com/post/how-to-fix-mysql-load-issues-on-mac-os-x](http://www.blog.bridgeutopiaweb.com/post/how-to-fix-mysql-load-issues-on-mac-os-x)
* [http://tjcjc.iteye.com/blog/766863](http://tjcjc.iteye.com/blog/766863)
* [http://blog.vkill.net/read.php/187.htm](http://blog.vkill.net/read.php/187.htm)
* [http://www.coreseek.cn/products-install/install_on_macosx](http://www.coreseek.cn/products-install/install_on_macosx)
