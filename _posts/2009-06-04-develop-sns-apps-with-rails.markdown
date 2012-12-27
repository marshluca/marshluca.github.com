---
layout: post
title: Rails开发校内网应用
category: Tech
tags: [Rails, sns]
---


去年底, 校内网推出了开放平台, 允许第三方开发者通过调用校内网接口, 创建第三方应用服务. 刚巧已经有人写好了基于Ruby的[xiaonei](http://github.com/taweili/renren)插件, 可以用来在校内网上构建rails应用. 这个插件已经封装好了校内网的开放平台接口, 能直接获取好友关系及其它数据.

下面简单记录下使用Rails开发校内应用的步骤:

1.申请开发校内应用

  填写基本信息, 然后创建即可. 应用创建成功后,会分配一个API Key 和API Secret, 这个在Rails应用配置中需要用到. 注意callback URL为服务器地址，cavas page按照提示选xnml.

2.创建rails应用

    rails sns
	
3.安装xiaonei插件

    git clone git://github.com/taweili/xiaonei.git
   
4.配置xiaonei.yaml

	development:
	  api_key: YOUR_API_KEY
	  secret_key: YOUR_SECRET_KEY
	  canvas_page_name: xuanjianghui
	  callback_url:http://61.172.255.117:8801
	  tunnel:
	    public_host_username: 
	    public_host: 
	    public_port: 4000
	    local_port: 3000

	test:
	  api_key: YOUR_API_KEY 
	  secret_key: YOUR_SECRET_KEY 
	  canvas_page_name: xuanjianghui
	  callback_url:http://61.172.255.117:8801
	
	production:
	  api_key: YOUR_API_KEY 
	  secret_key: YOUR_SECRET_KEY 
	  canvas_page_name: xuanjianghui
	  callback_url:http://58.215.65.224:4000


5.通过xiaonei插件获取校内网数据

	class ApplicationController < ActionController::Base 
	  helper :all        
	  acts_as_xiaonei_controller
	  before_filter :set_current_user
        
	  def set_current_user         
	    if @current_user
	     @current_user = SnsUser.find_by_xid(xiaonei_session.user)
		 # 确保session_key没有过期
	     if @current_user.session_key != xiaonei_session.session_key
	       @current_user.session_key = xiaonei_session.session_key
	       @current_user.save
	     end
	    end
	  end
	end

	class HomeController < ApplicationController  
	  def index   
	    # 获取当前用户的好友ids
	    result = xiaonei_session.invoke_method("xiaonei.friends.get")
	    @friend_ids = result unless result.kind_of?(Xiaonei::Error)
	  end
	end

6.在view中展示数据
	<%= debug @friend_ids %>

7.启动服务器

    ruby script/server 
	
然后直接用浏览器上访问 [http://apps.xiaonei.com/xuanjianhui/home/index](http://apps.xiaonei.com/xuanjianhui/home/index).

就可以看到你的好友ids列表了.