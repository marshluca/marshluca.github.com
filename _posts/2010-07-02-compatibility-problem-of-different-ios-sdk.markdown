---
layout: post
title: iOS SDK 各版本编译兼容性测试
category: iOS
tags: [iAd]
---


iOS4发布了, 对手上的一个项目用不同版本sdk编译后的兼容性做了下测试:

	xcode 3.2.2
	Base sdk: iPhone Device 3.1/iPhone Simulator 3.1
	iPhone OS Deployment Target: iPhone OS 3.1
	compile : Success
	MutipleTasks: NO


	xcode 3.2.3
	Base sdk : iPhone simulater 3.1/ iPhone device 3.1
	iPhone OS Deployment Target: iPhone OS 3.1
	compile: 38 errors
	MutipleTasks: NO


	xcode 3.2.3
	Base sdk : iPhone simulater 4.0/ iPhone device 4.0
	iPhone OS Deployment Target: iPhone OS 3.1
	compile: Success
	MutipleTasks: YES


	xcode 3.2.3
	Base sdk : iPhone simulater 4.0/ iPhone device 4.0
	iPhone OS Deployment Target: iPhone OS 4.0
	compile: Success
	MutipleTasks: YES


新版本对旧代码的编译都没问题, 向下兼容性做的很好.