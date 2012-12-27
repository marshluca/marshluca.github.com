---
title: 设计模式之Adapter
layout: post
category: Tech
tags: [设计模式, Ruby, Objective-C]
---


适配器模式的百度百科解释:

>在计算机编程中，适配器模式（有时候也称包装样式或者包装）将一个类的接口适配成用户所期待的。一个适配允许通常因为接口不兼容而不能在一起工作的类工作在一起，做法是将类自己的接口包裹在一个已存在的类中。

简单的说，就是需要的东西就在面前，但却不能使用，而短时间又无法改造它，于是我们就想办法适配它。

**1.Objetive-C 中的实现**

Target.h

	#import <Foundation/Foundation.h>                 

	@interface Target:NSObject

	-(void)request;

	@end


Target.m

	#import "Target.h"
               
	@implementation Target             

	-(void)request
	{
	    NSLog(@"普通请求！");
	}

	@end
	
Adaptee.h


	#import <Foundation/Foundation.h>             

	@interface Adaptee:NSObject             

	-(void)specificRequest;

	@end	
	
Adaptee.m

	#import "Adaptee.h"

	@implementation Adaptee

	-(void)specificRequest 
	{
	    NSLog(@"特殊请求！");
	}

	@end	
	
Adapter.h	

	#import "Target.h"

	@class Adaptee;

	@interface Adapter : Target {
	    Adaptee *_adaptee;
	}

    -(id)initWithAdaptee:(Adaptee *)adaptee;
	
	@end

Adapter.m

	#import "Adapter.h"
	#import "Adaptee.h"

	@implementation Adapter

	-(id)initWithAdaptee:(Adaptee *)adaptee
	{
	    if (self = [super init]) {
	        _adaptee = adaptee;
	    }

	    return self;
	}

	-(void)request 
	{
	    [_adaptee specificRequest];
	}

	@end
	
使用:

	Adaptee *adaptee = [[Adaptee alloc] init];
	Adapter *adapter = [[Adapter alloc] initWithAdaptee:adaptee];
	[adapter request];


**2.Ruby中的实现**

 Renderer 可直接渲染 TextObject 类，而 BritishTextObject 类具有不一样的接口。要使 Renderer 类也渲染
 BritishTextObject 类，则需要设计兼容的适配器 BritishTextObjectAdapter:
 
	class Renderer
	  def render(text_object)
	    text = text_object.text
	    size = text_object.size
	    puts "text:#{text}, size:#{size}\n"
	  end
	end
	
	class TextObject
	  attr_reader :text, :size
	  
	  def initialize(text, size)
	    @text = text
	    @size = size
	  end
	end
	
	class BritishTextObject
	  attr_reader :string, :length
	  
	  def initialize(string, size_mm)
	    @string = string
	    @size_mm = size_mm
	  end
	end
	
	class BritishTextObjectAdapter < TextObject
	  def initialize(british_text_object)
	    @british_text_object = british_text_object
	  end
	  
	  def text
	    return @british_text_object.string
	  end
	  
	  def size
	    return @british_text_object.size_mm/25.4
	  end
	end
	
	Renderer.new.render(TextObject.new("TextObject", 15)) 
	Renderer.new.render(BritishTextObjectAdapter.new(BritishTextObject.new("BritishObject", 15))) 
	
