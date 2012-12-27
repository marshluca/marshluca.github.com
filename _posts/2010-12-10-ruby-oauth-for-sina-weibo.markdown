---
layout: post
title: 新浪微博Ruby OAuth认证
category: Tech
tags: [Ruby, OAuth]
---

 OAuth认证主要包括以下四步内容
 
	 1. 获取Request Token
	 2. 用户确认授权
	 3. 换取Access Token
	 4. 访问受限资源
 
以下是新浪微博的OAuth授权代码.
 
	require 'rubygems'     
	gem 'oauth','0.4.3'
	require 'oauth'
 
 
	API_KEY = "1860579545"
	API_KEY_SECRET = "d62c11d9ddaa25d55fc59b1835e73619"
	SITE = "http://api.t.sina.com.cn"
 
	# 1. 获取Request Token
	consumer = OAuth::Consumer.new(API_KEY, API_KEY_SECRET, { :site => SITE })
	request_token = consumer.get_request_token
	puts request_token.inspect
 
	# 2. 用户确认授权
	callback_url = "http://localhost.com/oauth"
	url = "#{request_token.authorize_url}&oauth_token_secret=#{request_token.secret}&oauth_callback=#{callback_url}"
	puts "请将下面url粘贴到浏览器中，并同意授权，同意后按任意键继续:\n#{url}"
	# response = Net::HTTP.get(URI.parse(url))
	# puts response.body
 
 
	sleep(30) # 将回调url里的oauth_verifier手动存到本地
 
	# 3. 通过授权码oauth_verfier换取access_token
	oauth_verifier = IO.readlines("verifier.txt").first.strip
	verify_hash = {:oauth_verifier => oauth_verifier}
	access_token = request_token.get_access_token(verify_hash)
 
	# access_token = OAuth::AccessToken.new(consumer, access_token.token, access_token.secret)
	# puts access_token.inspect                                      
 
	# 4. 通过access_token访问受限资源(API)
	response = access_token.get "/statuses/home_timeline.xml"
	puts response.body
