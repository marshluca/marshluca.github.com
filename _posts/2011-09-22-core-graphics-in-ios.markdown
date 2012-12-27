---
title: iOS中的图形与动画编程
layout: post
category: Tech
tags: [iOS, Core Graphics, Core Animation]
---

iOS的 Runtime 和  Cocoa 编程框架结合在一起后, 只需相当简单的代码就可以实现令人惊讶的图形和动画效果. iOS 中图形和动画编程用到的相关框架及技术有:

* UIKit: 高层次的框架, 允许创建视图, 窗口, 按钮及其他UI组件 
* Quartz 2D: iOS 上的主要绘图引擎, UIKit 就是使用 Quartz
* Core Graphics: 支持图形上下文绘制图像
* Core Animation: iOS上实现动画的框架


1.绘制文本

    // 设置颜色 
	UIColor *color = [UIColor colorWithRed:0.4 green:0.5 blue:0.8 alpha:0.3];
	[color set];
        
	// 设置字体
	UIFont *font = [UIFont fontWithName:@"HelveticaNeue-Bold" size:20.f];
	NSString *str = @"Only override drawRect if you perform custom drawing.";    
	
	// Drawing text
	CGSize size = [str sizeWithFont:font constrainedToSize:CGSizeMake(280, 480) lineBreakMode:UILineBreakModeWordWrap];	
	[str drawInRect:CGRectMake(20, 100, size.width, size.height) withFont:font lineBreakMode:UILineBreakModeWordWrap];

2.绘制图像

    // Drawing image
	UIImage *image = [UIImage imageNamed:@"xcode.png"];
	[image drawAtPoint:CGPointMake(0, 0) blendMode:kCGBlendModeNormal alpha:1.0];
	[image drawInRect:CGRectMake(100, 20, 200, 400) blendMode:kCGBlendModeNormal alpha:0.7];

3.绘制线条

	// Drawing Routes
	[[UIColor redColor] set];
    
	CGContextRef currentContext = UIGraphicsGetCurrentContext();
	CGContextSetLineWidth(currentContext, 10.f);    
	CGContextMoveToPoint(currentContext, 50.f, 50.f);    
	CGContextAddLineToPoint(currentContext, 100.f, 100.f);
	CGContextAddLineToPoint(currentContext, 300.f, 20.f);
	CGContextSetLineJoin(currentContext, kCGLineJoinBevel);
	CGContextStrokePath(currentContext);

4.构造路径

	// Drawing Line Path
	CGMutablePathRef path = CGPathCreateMutable();
	CGRect screenBounds = [[UIScreen mainScreen] bounds];
	CGPathMoveToPoint(path, NULL, screenBounds.origin.x, screenBounds.origin.y);
	CGPathAddLineToPoint(path, NULL, screenBounds.size.width, screenBounds.size.height);
	CGPathMoveToPoint(path, NULL, screenBounds.size.width, screenBounds.origin.y);
	CGPathAddLineToPoint(path, NULL, screenBounds.origin.x, screenBounds.size.height);
    
	CGContextRef currentContext = UIGraphicsGetCurrentContext();
	CGContextAddPath(currentContext, path);
	[[UIColor blueColor] setStroke];
	CGContextDrawPath(currentContext, kCGPathStroke);
	CGPathRelease(path);

5.绘制矩形路径

	// Drawing Rect Path
	CGMutablePathRef path = CGPathCreateMutable();
	CGRect rectangle = CGRectMake(50.0f, 50.0f, 200.0f, 300.0f);
	CGPathAddRect(path, NULL, rectangle);
    
	[[UIColor redColor] setFill];
	[[UIColor blueColor] setStroke];
    
	CGContextRef currentContext = UIGraphicsGetCurrentContext();
	CGContextSetLineWidth(currentContext, 20);
	CGContextSetLineJoin(currentContext, kCGLineJoinRound);
	CGContextSetLineCap(currentContext, kCGLineCapSquare);
	CGContextSetShadowWithColor(currentContext, CGSizeMake(10.0f, 10.0f), 20.0f, [[UIColor grayColor] CGColor]);
    
	CGContextAddPath(currentContext, path);
	CGContextDrawPath(currentContext, kCGPathFillStroke);
	CGPathRelease(path);
