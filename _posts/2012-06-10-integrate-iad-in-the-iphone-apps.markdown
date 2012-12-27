---
layout: post
title: Integrate iAd banner view in the iPhone Apps
category: iOS
tags: [iAd, iOS]
---


Apple has released a new feature on advertisement platform called iAd. It allows developers to earn on their Apps by showing advertisement. Apple also said iAd can be interactivity and emotionality, and it can support some awesome advertisement which looks like apps with video and audio. In addition, iAd advertisement is opened inside the app, you do not need exit the app and can get back whenever you wants. 

Yes, it sounds great, but how to integrate a iAd banner view in the iPhone Apps? Below is a basic coding guide on it.

1.Declare your iAd banner view, and you also need a boolean variable to decide whether to display it.

	#import <UIKit/UIKit.h>;
	#import <iAd/iAd.h>;
 
	@interface iAdViewController : UIViewController <ADBannerViewDelegate>
	{
	   ADBannerView *adView;
	   BOOL bannerIsVisible;
	}
	@property (nonatomic,assign) BOOL bannerIsVisible;
	@end


2.In the iAdViewController.m, you can modify viewDidLoad method to initialize a AdBannerView instance, and remember to set a delegate to it.

	- (void)viewDidLoad
	{
	  [super viewDidLoad];
  
	  adView = [[ADBannerView alloc] initWithFrame:CGRectZero];
	  adView.frame = CGRectOffset(adView.frame, 0, -50);
	  adView.requiredContentSizeIdentifiers = [NSSet setWithObject:ADBannerContentSizeIdentifier320x50];
	  adView.currentContentSizeIdentifier = ADBannerContentSizeIdentifier320x50;
	  [self.view addSubview:adView];
	  adView.delegate=self;
	  self.bannerIsVisible=NO;
	}

3.Yes, you also need to implement the ADBannerViewDelegate methods in iAdViewController.m.

	- (void)bannerViewDidLoadAd:(ADBannerView *)banner
	{
	  if (!self.bannerIsVisible)
	  {
		 [UIView beginAnimations:@"animateAdBannerOn" context:NULL];
	     // banner is invisible now and moved out of the screen on 50 px
	   	 banner.frame = CGRectOffset(banner.frame, 0, 50);
	     [UIView commitAnimations];
	     self.bannerIsVisible = YES;
	  }
	}

	- (void)bannerView:(ADBannerView *)banner didFailToReceiveAdWithError:(NSError *)error
	{
	  if (self.bannerIsVisible)
	  {
	    [UIView beginAnimations:@"animateAdBannerOff" context:NULL];
	   	// banner is visible and we move it out of the screen, due to connection issue
	    banner.frame = CGRectOffset(banner.frame, 0, -50);
	    [UIView commitAnimations];
	    self.bannerIsVisible = NO;
	  }
	}

4.Launch the App, you will see the iAd banner view on the top. Click it, a test advertisement will be popup in the entire screen.