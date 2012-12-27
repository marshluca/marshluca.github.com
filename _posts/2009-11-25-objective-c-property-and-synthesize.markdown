---
layout: post
title: Objective-C中的property和synthesize
category: Tech
tags: [Objective-C]
---


@property和@synthesize是Objective-C 2.0之后加进来的预编译命令, 作用是自动声明属性的setter和getter方法.

在Objective-C 2.0之前, 如果要给一个对象声明属性, 大概思路是这样的:

    // User.h

	@interface User : NSObject
	{ 
	    NSString *name; 
	} 

	- (NSString*)name; 
	- (void)setName:(NSString*)aName; 

	@end 


	// User.m 
	
	#import "User.h"
	@implementation User
	
	- (NSString*)name 
	{ 
	    return name; 
	} 

	- (void)setName:(NSString*)aName 
	{ 
	    [name autorelease]; 
	    name = [aName copy]; 
	} 
	
    @end


Objective-C 2.0之后可以简化成这样


	// User.h

	@interface User : NSObject
	{ 
	    NSString *name; 
	} 

	@property(nonatimic, copy) NSString *name; 

	@end 

	// User.m 
	#import "User.h" 

	@implementation User 

	@synthesize name; 

	@end 
	
访问	name 属性

	User *user = [[User alloc] init];
	user.name = @"Apple";    // setter方法
	NSLog(@"%@", user.name); // getter方法
