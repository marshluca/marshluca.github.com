---
layout: post
title: iPhone Apps中跳转到苹果自带的其他应用
category: iOS
tags: []
---



邮件：

	NSURL *url = [NSURL URLWithString:@"mailto:steve@apple.com"];
	[[UIApplication sharedApplication] penURL:url];

电话：

    NSURL *url = [NSURL URLWithString:@"tel://8004664411"];
	[[UIApplication sharedApplication] openURL:url];

短信：

    NSURL *url = [NSURL URLWithString:@"sms://466453"];
	[[UIApplication sharedApplication] openURL:url];

浏览器：

    NSURL *url = [NSURL URLWithString:@"http://www.apple.com"];
	[[UIApplication sharedApplication] openURL:url];
