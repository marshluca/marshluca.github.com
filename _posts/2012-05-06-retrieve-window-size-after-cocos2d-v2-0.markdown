---
layout: post
title: "Retrieve window size after Cocos2d v2.0"
category: iOS
tags: [Cocos2d, iOS]
---



If you call **[[CCDirector sharedDirector] winSize]** when initialized in Cocos2d v2.0 above, you will get a window size in portrait when in fact it is in landscape.

	- (id)init
	{
	    if (self = [super init])
	    {
	        self.isTouchEnabled = YES;
		
			CGSize winSize = [[CCDirector sharedDirector] winSize];		
			CCLog(@"%f, %f", winSize.width, winSize.height);
	    }
    
	    return self;
	}
	

The bug is related to some of the following changes that were done in v2.0:

* CCDirector is a subclass of ViewController
* The rotation is handled by the ViewController... no more auto-rotation in OpenGL
* The tests and templates have one additional controller: UINavigationController that is very handy to integrate cocos2d with UIKit components.
* And these controller is being added to the main UIWindow with setRootViewController.


You can fix it by calling winSize method inside onEnter:

	- (void) onEnter
	{
	    [super onEnter];
    
		CGSize winSize = [[CCDirector sharedDirector] winSize];		
		CCLog(@"%f, %f", winSize.width, winSize.height);
	}
