--- 
layout: post
title: 淘宝 Ruby API 使用示例
wordpress_id: 127
wordpress_url: http://marshluca.com/?p=127
date: 2010-02-23 16:39:23 +08:00
---

此DEMO的功能：查询手机类的淘客商品
	
	require 'digest/md5'
	require 'net/http'
	require 'open-uri'
	require 'iconv'
 
	#获得当前时间
	t = Time.new();
 
	#组装参数
	paramArray = {
		#组装协议参数
	  'app_key'=>'test',
	  'method'=>'taobao.taobaoke.items.get',
	  'format'=>'json',
	  'v'=>'2.0',
	  'timestamp'=>t.strftime("%Y-%m-%d %H:%M:%S"),
	  #组装应用参数
	  'fields'=>'iid,title,nick,pic_url,price',
	  'pid' => 'mm_5410_0_0',
	  'cid' => '1512',
	  'page_no' => '1',
	  'page_size' => '20'
	}
	#生成签名
	def sign(params)
	  Digest::MD5.hexdigest(params.sort().collect{|key,value| key+value}.join).upcase
	end
 
	#组装请求参数
	def createRequestParam(params)
	  params.sort().collect{|key,value| key+"="+value+"&"}.join
	end
 
	#URL encode
	def URLEncode(str)
		return str.gsub!(/[^\w$&\-+.,\/:;=?@]/) { |x| x = format("%%%x", x[0])}
	end
 
	#把str的编码转化为GBK编码
	def to_gbk(str)
		Iconv.iconv("GBK//IGNORE","UTF-8//IGNORE",str).to_s
	end 
 
	url = 'http://gw.api.tbsandbox.com/router/rest?'
	url << createRequestParam(paramArray) << 'sign=test' << sign(paramArray)
	parsedURL = URI.parse(URLEncode(url))
                      
	puts parsedURL
	#请求生成的URL，把结果输出
	Net::HTTP.version_1_2
	open(parsedURL) do |http|
	  # puts to_gbk(http.read)
	  puts http.read
	end
