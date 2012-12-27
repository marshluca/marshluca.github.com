---
title: 设计模式之Strategy
layout: post
category: Tech
tags: [设计模式, Ruby]
---

百度百科策略模式的定义:

> 策略模式定义了一系列的算法，并将每一个算法封装起来，而且使它们还可以相互替换。策略模式让算法独立于使用它的客户而独立变化。

定义了一个公共接口，各种不同的算法以不同的方式实现这个接口。


**Ruby中的实现思路**

有 HtmlFormatter 和 TextFormatter 两种格式化的策略，可在 Report 类中进行替换。

	class Formatter
	  def format(text)
	    puts "#{format_start}#{format_body(text)}#{format_end}"
	  end
	  
	  def format_body(text)
	    "\ntext\n"
	  end
	end
	
	class HtmlFormatter < Formatter
	  def format_start
	    %(<html>)
	  end
	  
	  def format_end
	    %(</html>)
	  end
	end
	
	class TextFormatter < Formatter
	  def format_start
	    %(===start===)
	  end
	  
	  def format_end
	    %(===end===)
	  end
	end
	
	class Report
	  def initialize(formatter)
	    @formatter = formatter
	    @text = "report"
	  end
	  
	  def output
	    @formatter.new.format(@text)
	  end
	end
	
	Report.new(HtmlFormatter).output
	Report.new(TextFormatter).output