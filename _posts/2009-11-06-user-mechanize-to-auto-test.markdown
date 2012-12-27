---
layout: post
title: Mechanize模拟浏览器操作
category: Tech
tags: [Ruby, Mechanize]
---


Ruby里面有个Mechanize插件, 可以模仿浏览器的基本操作, 比方点击事件, 提交表单等. 用来做数据爬取或是自动化测试非常方便.

下面是豆瓣自动顶贴的代码片段, 里面有mechanize的基本用法.


	require 'rubygems'
	require 'mechanize'
    
	def comment_douban_topic(url, text)
	  login_url = 'http://www.douban.com/login'
	  agent = Mechanize.new
	  # set proxy
	  # agent.set_proxy(218.201.21.176, 80)
	  # set user agent
	  agent.user_agent_alias = 'Googlebot'
	  page = agent.get(login_url)
                       
	  form = page.forms.last
	  form.form_email = 'your email'
	  form.form_password = 'your password'
	  page = agent.submit(form)
	  # puts agent.cookie_jar.inspect
                                  
	  page = agent.get(url)
	  comment_form = page.forms.last
	  comment_form.rv_comment = text
	  page = agent.submit(comment_form)  
	  # puts page.body
	end


	topic_url = 'http://www.douban.com/group/topic/1105027/?start=200'
	comment_content = '不错, 支持一下~'
	comment_douban_topic(topic_url,comment_content)

参考:

* [http://mechanize.rubyforge.org/](http://mechanize.rubyforge.org/)
* [https://github.com/sparklemotion/nokogiri](https://github.com/sparklemotion/nokogiri)