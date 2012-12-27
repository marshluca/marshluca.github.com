---
title: 屏蔽Rails中的Asset Pipeline日志
layout: post
category: Tech
tags: [Rails, Asset Pipeline]
---


Rails3应用中如果开启了 AssetPipeline 功能, 日志中会出现很多静态资源的相关请求. 

	Started GET "/assets/application.css" for 127.0.0.1 at 2011-06-10 17:30:45 -0400
	Compiled app/assets/stylesheets/application.css.scss  (5ms)  (pid 6303)

	Started GET "/assets/application.js" for 127.0.0.1 at 2011-06-10 17:30:45 -0400
	Compiled app/assets/stylesheets/default.css.scss  (15ms)  (pid 6303)

	Started GET "/assets/default/header_bg.gif" for 127.0.0.1 at 2011-06-10 17:30:45 -0400
	Served asset /default/header_logo.gif - 304 Not Modified  (7ms)  (pid 6303)
	Served asset /default/header_bg.gif - 304 Not Modified  (0ms)  (pid 6246)
	Served asset /default/footer_bg.gif - 304 Not Modified  (49ms)  (pid 6236)

如果想在开发模式下屏蔽这些静态资源请求日志, 可以这样来解决:

	# Place the following code in config/initializers/quiet_assets.rb
 
	if Rails.env.development?
  	  Rails.application.assets.logger = Logger.new('/dev/null')
  	  Rails::Rack::Logger.class_eval do
	    def before_dispatch_with_quiet_assets(env)
	      before_dispatch_without_quiet_assets(env) unless env['PATH_INFO'].index("/assets/") == 0
 	    end
	    alias_method_chain :before_dispatch, :quiet_assets
	  end
	end
	

