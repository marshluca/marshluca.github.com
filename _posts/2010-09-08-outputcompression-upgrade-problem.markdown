---
layout: post
title: output_compression gem升级问题
category: Programming
tags: [Rails, gem]
---


最近把公司的API项目升到 Rails 2.3.8 上了, 升级的时候 output_compression 插件出现了不兼容问题.


> undefined method `is_component_request?' for #<ActionController::Request:0x78fcb0c>

原因是 Rails 2.3.0 发布的时候, 底层的架构做了较大的[调整变化](http://guides.rubyonrails.org/2_3_release_notes.html). 以下是导致升级问题的更新:

* 集成了 Rack, 原先CGI模块下面的类已经改名
* ActionController的调整


下面是修改之后的 lib/output_compression.rb 核心代码:

	# Handle component requests by not compressing the output from a component
	module Rack
	  # These methods are available in both the production and test Request objects.
	  class ActionController::Request
	    def is_component_request=(val)  #:nodoc:
	      @is_component_request = val
	    end

	    # Returns true when the request corresponds to a render_component call
	    def is_component_request?
	      @is_component_request
	    end
	  end
	end

	# Mark the request as being a Component request
	module Rack
	  module Components
	    protected
	      def request_for_component(options)
	        request_for_component = original_request_for_component(options)
	        request_for_component.is_component_request = true
	        return request_for_component
	      end
	      alias :original_request_for_component :request_for_component
	  end
	end
	
output_compression: 
<a href="http://github.com/shad/output_compression" target="_blank">http://github.com/shad/output_compression</a>
