---
layout: post
title: 使用Ultrasphinx进行全文检索
category: Tech
tags: [Rails, Ultrasphinx]
---

Ultrasphinx是一个Rails插件，它使得Rails应用可以非常方便地访问Sphinx的searchd服务实现全文检索功能。在Ultrasphinx的内部使用Riddle来访问Sphinx，Riddle是第三方开发的Sphinx的Ruby API。Riddle直接通过socket来访问searchd的服务，它和search程序一样是searchd的一个客户端，只不过它是以API方式提供的。 
Sphinx的安装见前面一篇文章。

####安装Ultrasphinx

	gem install chronic 	
	gem install ultrasphinx
	
如果gem安装有问题, 可以试试script/plugin方式	

	ruby script/plugin install svn://rubyforge.org/var/svn/fauna/ultrasphinx/trunk      

####配置Model中需要索引的字段

   通过is_indexed方法告诉ultrasphinx

	class Place < ActiveRecord::Base

	  is_indexed :fields =>[:name]

	end

####生成ultrasphinx配置

     rake ultrasphinx:configure
	 
   在config/ultrasphinx/目录下会生成一个新的development.conf. 如果要在production模式下, 需要加上环境变量 
   
     RAILS_ENV=production rake ultrasphinx:configure   
	 
####对数据进行索引
    
	rake ultrasphinx:index
	
####启动搜索服务

这个命令会启动sphinx的searchd服务

    rake ultrasphinx:daemon:start
	
下面是停止, 重启的脚本

    rake ultrasphinx:daemon:stop
    rake ultrasphinx:daemon:restart	
			
