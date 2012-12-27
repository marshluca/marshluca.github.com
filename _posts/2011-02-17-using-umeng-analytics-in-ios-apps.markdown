--- 
layout: post
title: iOS项目中加入友盟统计分析SDK
category: iOS
tags: [UMeng, iOS]
---

最近需要研究一款应用的用户使用习惯，而目标群体主要集中在国内，于是使用到了友盟团队的iOS统计分析SDK。
通过提供的Demo,封装了一个专门的类来统计相关view的启动，和指定action的执行情况.

UMeng.h

	#import <Foundation/Foundation.h>
	#import "MobClick.h"

	@interface UMeng : NSObject <MobClickDelegate> {
	}

	- (id)init;
	- (void)appLaunched;
	- (void)appTerminated;
	- (void)event:(NSString *)event;
	- (void)event:(NSString *)event label:(NSString *)label;

	@end


UMeng.m

	#import "UMeng.h"

	@implementation UMeng

	- (id)init
	{
		if (self = [super init]) {		
		}
		
		return [self autorelease];
	}

	// 启动统计
	- (void)appLaunched
	{
		[MobClick setDelegate:self];
		[MobClick appLaunched];
	}

	// 退出统计
	- (void)appTerminated
	{
		[MobClick appTerminated];
	}

	// 事件统计
	- (void)event:(NSString *)event
	{
		[MobClick event:event];
	}

	// 标签事件统计
	- (void)event:(NSString *)event label:(NSString *)label
	{
		[MobClick event:event label:label];
	}


	#pragma mark  MobClickDelegate methods
	- (NSString *)appKey{
		return "your app key";
	}

	- (NSString *)channelId{
		return @"App Store";
	}


	- (void)dealloc 
	{
		[super dealloc];
	}

	@end


需要的执行统计的地方，可以通过以下方式调用：

	[[[UMeng alloc] init] appLaunched];

如果是在非real time模式下，所有被统计的请求会在App的下一次启动的时候，被发送到服务器.
