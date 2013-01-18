---
title: Use CocoaPods to manage library dependencies in iOS projects
layout: post
category: Tech
tags: [CocoaPods, iOS]
---



If you are a Ruby developer, you may heard of [Bundler](http://gembundler.com/), an awesome tool to manage gem dependencies in Ruby Applications. [Cocoapods](https://github.com/CocoaPods/CocoaPods) built with Ruby and inspired from Bundler, which aims to improve discoverability of, and engagement in, third party open-source libraries, by creating a more centralized ecosystem.

At past, if you use a lot of open source libraries with great quality that can help you build your application, you may need download the library first, and copy it into your project, then you can declare and use it. But there are still some disadvantages. The dependencies stored in your project will take in some space, and it is unconvinient to upgrade when there is an available update, because you can not specify an version of it.  

All of the problems mentioned above can be resolved with a dependencies manager. CocoaPods resolves dependencies between libraries, fetches source code for the dependencies, and creates and maintains an Xcode workspace to build your project.

#### Installation

To install it, you just need to run the below command in terminal, but make sure you have made Ruby installed first.

	gem install cocoapods


#### Use the remote libraries

In the root directory of your iOS projects, add a `Podfile` and edit it

	platform :ios

	pod 'AFNetworking', '1.1.0'
	pod 'JSONKit', '1.4'

then run the command:

    pod install
	
it will create `Pods` and `APP.xcworkspace` directories, and there is also a message followed:

	[!] From now on use `APP.xcworkspace'	    
	
what you need is open `APP.xcworkspace`, remember always to open the Xcode workspace instead of the project file. After open it, keep `Pods` scheme selected, and build away both in iOS Simulator and Device mode by clicking `Commond + B`.  Now, switch scheme to `APP`, you can run the application in your simulator or device.


### Use a local library

You can find all the repositories are managed by [CocoaPods/Specs](https://github.com/CocoaPods/Specs), but if the open source library hosted on Github is not included in CocoaPods/Specs, how to install it to the project ? Below is a basic steps for setting up a local library with CocoaPods:

1.choose a place wherever you like to store your third party library in your computer 

	$ mkdir -p ~/code/Pods/NAME
	$ cd ~/code/Pods/NAME
	$ pod spec create NAME

2.edit NAME.podspec, you can view the full document on podspec format [here](https://github.com/CocoaPods/CocoaPods/wiki/The-podspec-format), and there are also some podspec [examples](https://github.com/CocoaPods/CocoaPods/wiki/Examples-of-specifications).

3.lint the pod against the files of its directory

	$ pod spec lint --local
	
4.Now, you can add the library to `Podfile`	

	platform :ios

	pod 'AFNetworking', '1.1.0'
	pod 'JSONKit', '1.4'    
	pod 'NAME', :local => '~/code/Pods/NAME'
	
5.Run `pod install`	again, and open `APP.xcworkspace`,  you will find a `Local Pods` directory there. After compile it again, you can install the application to your simulator or device.

Of course, you can share your code and contribute it to [CocoaPods/Specs](https://github.com/CocoaPods/Specs), just follow this [guide](https://github.com/CocoaPods/CocoaPods/wiki/Contributing-to-the-master-repo).