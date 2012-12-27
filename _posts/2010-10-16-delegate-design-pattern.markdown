---
title: 设计模式之Delegate
layout: post
category: Tech
tags: [设计模式, Ruby, Objective-C]
---

代理模式在有的地方叫委托模式, 是软件设计模式中的一项基本技巧。

>在委托模式中，有两个对象参与处理同一个请求，接受请求的对象将请求委托给另一个对象来处理。

说白了, 就是为其他对象提供一个代理, 以控制对这个对象的访问，降低对象间的耦合. 

在Objective-C中, delegate模式是很常见的, 像UITableViewDelegate, UIScrollViewDelegate等都是, 实现起来也是非常简单.

例如, 点击了屏幕上的VideoView后, 可以告诉它的代理对象做一些更新, 比方改变屏幕背景颜色.

VideoView.h

	#import <UIKit/UIKit.h>

	@protocol VideoViewDelegate;

	@interface VideoView : UIView
	{
    	id<VideoViewDelegate> delegate;
	}

	@property (assign) id<VideoViewDelegate> delegate;

	@end

	@protocol VideoViewDelegate <NSObject>
	@required
	- (void)touchVideoView:(VideoView *)videoView;
	@end


VideoView.m

	#import "VideoView.h"

	@implementation VideoView

    #pragma mark
	#pragma mark Touch Events

	- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event
	{
	    if (self.delegate && [delegate respondsToSelector:@selector(touchVideoView:)]) 
	    {
	        [delegate touchVideoView:self];
	    }
	}

	@end
	
VideoViewController.h 中声明代理协议:
	
	#import "VideoView.h"

	@interface VideoViewController : UIVIewController <VideoViewDelegate>
	{
	}

	@end
	
	
VideoViewController.m 中初始化videoView, 并给其设置delegate对象:

	#import "VideoViewController.h"

	@implementation VideoViewController

	- (void)viewDidLoad
	{
	    [super viewDidLoad];
	    // Do any additional setup after loading the view from its nib.
    
	    CGRect rect = CGRectMake(50, 50, 100, 100);
		VideoView *videoView = [[VideoView alloc] initWithFrame:rect];
		videoView.backgroundColor = [UIColor redColor];
		videoView.delegate = self;
		[scrollView addSubview:videoView];
	}

    #pragma mark
	#pragma mark Video Vide Delegate

	- (void)touchVideoView:(VideoView *)videoView
	{
	    // do something when touch vide view
		self.view.backgroudColor = [UIColor blueColor];
	}

	@end	

在Rails中, delegate被作为Module的类方法集成到了ActiveSupport中:

	class User < ActiveRecord::Base
	  has_many :actions 
	  delegate :inbox, :to => :actions
	end
 
	class Action < ActiveRecord::Base
	  belongs_to :user
 
	  class << self
	    def inbox
	      find(:all, :conditions => ['inbox = 1'])
	    end
	  end
	end
 
	user = User.find(1)
	user.inbox # instead of user.actions.inbox

参考: [http://api.rubyonrails.org/classes/Module.html#method-i-delegate	](http://api.rubyonrails.org/classes/Module.html#method-i-delegate	)