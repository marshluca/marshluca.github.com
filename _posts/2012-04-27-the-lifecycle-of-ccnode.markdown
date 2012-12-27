---
layout: post
title: "The lifecycle of CCNode"
category: iOS
tags: [Cocos2d, iOS]
---



CCNode have three methods on scene managment:

	@interface NSNode
	- (void)onEnter;
	- (void)onEnterTransitionDidFinish;
	- (void)onExit;
	@end

When you call replaceScene with a CCDirector instance, the three methods will be revoked. The general order of the execution likes below:

	scene: OtherScene
	init: <OtherScene = 07690B30 | Tag = -1>
	onExit: <FirstScene = 0D85C430 | Tag = -1>
	dealloc: <FirstScene = 0D85C430 | Tag = -1>
	onEnter: <OtherScene = 07690B30 | Tag = -1>
	onEnterTransitionDidFinish: <OtherScene = 07690B30 | Tag = -1>

The first scene will be relased before the second call onEnter method. But when the CCNode enters the stage with a transition, for example:

	CCSlideInBTransition* transition = [CCSlideInBTransition transitionWithDuration:3 scene:[OtherScene scene]];
	[[CCDirector sharedDirector] replaceScene:transition];

you will get another result:

	scene: OtherScene 
	init: <OtherScene = 08BC4640 | Tag = -1>
	onEnter: <OtherScene = 08BC4640 | Tag = -1>
	onExit: <FirstScene = 0E158AC0 | Tag = -1>
	onEnterTransitionDidFinish: <OtherScene = 08BC4640 | Tag = -1>
	dealloc: <FirstScene = 0E158AC0 | Tag = -1>

The first scene will be release after the second call onEnterTransitionDidFinish method.
