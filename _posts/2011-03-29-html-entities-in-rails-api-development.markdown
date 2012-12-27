---
layout: post
title: Rails开发API时的HTML Entities问题
category: Ruby
tags: [Rails, XML, JSON]
---

API 开发时, 数据通信一般会选择 XML 作为载体. 在Rails3 中, Builder作为默认的XML构造器.

	require 'rubygems'
	require 'builder'

	xml = Builder::XmlMarkup.new(:target => $stdout, :indent => 2)
	xml.instruct!
	xml.comment! 'greetings'
	xml.hello('你好', :name => 'hello')
	
	puts xml.inspect

得到:

	<xml version="1.0" encoding="UTF-8"> 
	<!-- greetings --> 
	<hello name="hello">&#20320;&#22909;</hello> 	
	</xml>
	
你会发现, 正常的中文字符在浏览器中被转成了HTML实体(HTML Entities).  那么如何让浏览器看到正常的中文字符呢? 打个 monkey patch 就可以了:
	
	module Builder
	  class XmlBase
	    def _escape(text)
	      text
	    end
	  end
	end

将上面的代码放到 config/initializers/xml_escape.rb 里, 让Rails初始化的时候就去加载它.

**补充:**

API 通过 JSON 做数据通信的时候也碰到了这个问题, 下面是针对 JSON 的 monkey patch:

	# Place the following code in config/initializers/json_escape.rb
	module ActiveSupport
	  module JSON
	    module Encoding
	      def self.escape(string)
	        if string.respond_to?(:force_encoding)
	          string = string.encode(::Encoding::UTF_8, :undef => :replace).force_encoding(::Encoding::BINARY)
	        end
	        json = string.gsub(escape_regex) { |s| ESCAPED_CHARS[s] }
	        json = %("#{json}")
	        json.force_encoding(::Encoding::UTF_8) if json.respond_to?(:force_encoding)
	        json
	      end
	    end
	  end
	end
