---
title: 设计模式之Singleton
layout: post
category: Tech
tags: [设计模式, Ruby, Objective-C]
---

单例模式是设计模式中常见的一种, 百度百科的解释:

>单例模式的意思就是只有一个实例。单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例。这个类称为单例类。


目的是希望类只创建一个实例，并且提供一个全局的访问点。单例和全局变量的区别在于单例是为了只发生一次的事情而建模。

例如, 要在项目中提供一个数据库类的全局访问实例:

**1. Objective-C中实现方式:**

Database.h

	#import <UIKit/UIKit.h>

	@interface Database : NSObject {
	}

	+ (Database *)sharedDatabase;

	@end

Database.m

    #import "Database.h"
	
    @implementation Database
	
	static Database *sharedDatabase = nil;
 
	+ (Database *)sharedDatabase
	{
	    @synchronized(self)
	    {
	        if(sharedDatabase == nil) {
	            sharedDatabase = [[self alloc] init];
	        }
	    }
    
	    return sharedDatabase;
	}
 
	+ (id)alloc
	{
	    @synchronized(self)
	    {
	        if (sharedDatabase == nil) {
	            sharedDatabase = [super alloc];
	        }
        
	        return sharedDatabase;
	    }
    
	    return nil;
	}
	
	@end

使用方式:

	Database *db = [Database sharedDatabase];
	
**2. Ruby 中实现方式**

第一种思路是直接借助Ruby标准库里的Singleton模块:

    require 'rubygems'
	require 'singleton'

	class Database
	  include Singleton
	end

	db = Database.new      # NoMethodError: private method `new' called for Database:Class
	db = Database.instance  
	
	
第二种思路是自己动手实现:

	class Database
	  @@instance = Database.new

	  def self.instance
	    return @@instance
	  end

	  private_class_method :new
	end

    db = Database.new      # NoMethodError: private method `new' called for Database:Class
	db = Database.instance