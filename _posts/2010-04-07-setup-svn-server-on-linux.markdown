---
layout: post
title: Linux服务器上搭建SVN服务
category: Programming
tags: [svn, Linux]
---


在Linux服务器上搭建 SVN 服务其实非常简单, 全部步骤如下:


    # 创建仓库根目录
	$ mkdir svn && cd svn	
	# 生成项目仓库
	$ svnadmin create repo
	# 启动svnserve服务
	$ sudo svnserve -d -r ~/svn/
    
	# 本地克隆项目
	$ svn co svn://127.0.0.1/repo

如果要设置权限和密码，编辑svnserve.conf:

	$ sudo vi ~/svn/repo/conf/svnserve.conf

改为：

	[general]
	anon-access = none
	password-db = passwd

并编辑 ~/svn/simple/conf/passwd 文件

	$ sudo vi ~/svn/repo/conf/passwd

配置密码：(明文密码)

	[users]
	user1 = password1 
	user2 = password2

克隆项目:

	$ svn co svn://127.0.0.1/repo --username user1


关于 sudo svnserve -d -r ~/svn/ 

* -d 做为后台服务运行，要停止直接用 sudo killall svnserve 
* -r 表示将 ~/svn/ 作为根目
