---
layout: post
title: 去掉UIWebView底部的阴影
category: Tech
tags: [iOS, UIWebView]
---

上下滑动UIWebView的时候, 会发现底部带有黑色的阴影. 

下面是去掉阴影的代码片段:


	- (void)viewDidLoad
	{
	  [super viewDidLoad];

	  [webView setBackgroundColor:[UIColor clearColor]];
	  [webView loadHTMLString:@"This is a completely transparent UIWebView." baseURL:nil];
	  [self hideGradientBackground:webView];
	}

	- (void) hideGradientBackground:(UIView*)theView
	{
	  for (UIView * subview in theView.subviews)
	  {
	    if ([subview isKindOfClass:[UIImageView class]])
	      subview.hidden = YES;
     	  
		[self hideGradientBackground:subview];
	  }
	}

