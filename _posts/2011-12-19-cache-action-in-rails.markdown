---
title: Rails中的caches_action
layout: post
category: Tech
tags: [Rails, 缓存]
---


	class ListsController < ApplicationController
	  before_filter :authenticate, :except => :public
 
	  caches_page :public
 
	  caches_action :index, :if => proc do
	    !request.format.json?  # cache if is not a JSON request
	  end
 
	  caches_action :show, :cache_path => { :project => 1 },
	    :expires_in => 1.hour, :layout => false
 
	  caches_action :feed, :cache_path => proc do
	    if params[:user_id]
	      user_list_url(params[:user_id, params[:id])
	    else
	      list_url(params[:id])
	    end
	  end
	end
	
1. if 选项可以接受一个proc 作为cache的条件
2. 通过 :layout => false 只缓存 action 的内容
3. expires_in 是设置缓存的时间