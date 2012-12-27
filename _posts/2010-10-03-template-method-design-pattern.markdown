---
title: 设计模式之Template Method
layout: post
category: Tech
tags: [设计模式, Ruby]
---


> 定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。
 模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

 TextReport 和 HtmlReport 类都继承自 Report 类. output 即是他们公用的模板方法。

	class Report
	
	  def output
	    puts "#{reprot_start}#{reprot_body}#{reprot_end}"
	  end
	  
	  def reprot_body
	    %(\nbody\n)
	  end
	end
	
	class HtmlReport < Report
	  def reprot_start
	    %(<html>)
	  end
	  
	  def reprot_end
	    %(</html>)
	  end
	end
	
	class TextReport < Report
	  def reprot_start
	    %(===start===)
	  end
	  
	  def reprot_end
	    %(===end===)
	  end
	end
	
	TextReport.new.output
	HtmlReport.new.output
