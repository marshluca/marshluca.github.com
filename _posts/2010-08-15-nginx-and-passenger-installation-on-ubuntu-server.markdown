---
layout: post
title: Ubunt服务器上搭建Nginx和Passenger环境
category: Programming
tags: [Nginx, Passenger, Ubuntu]
---


环境：Ubuntu Server 10.4 

一. 安装passenger 
    
	sudo gem install passenger  


二.通过passenger安装配置nginx,默认会将nginx安装到/opt/nginx下 

	sudo passenger-install-nginx-module  


三.nginx启动脚本 

1.写启动脚本到：/etc/init.d/nginx,  sinnpets 在这里：http://gist.github.com/525330 

2.权限控制：    

	chmod +x /etc/init.d/nginx  

3.注册启动脚本： 
   
	/usr/sbin/update-rc.d -f nginx defaults  

4.启动nginx服务： 
   
	/etc/init.d/nginx start  


四.配置Rails application 
   编辑/opt/nginx/conf/nginx.conf

	server {  
	  listen 81;  
	  server_name localhost;  
	  rails_env development;  
	  root /home/ubuntu/app/public;  
	  passenger_enabled on;  
	}  
