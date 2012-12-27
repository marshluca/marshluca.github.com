---
layout: post
title: "Build and integrate a static library in iOS projects"
category: iOS
tags: [iOS]
---



When we are developing in iOS projects, there may be some modules can be reused in other projects. But because of the external frameworks and dynamic library will be rejected by Apple Review Guidelines when submitted to App Store. In order to save our development time, we need to compile a static library and then integrate it into other projects.

Below I will introduce the basic steps on how to implement it.

* Create a new project by choosing a Cocoa Touch Static Library template. You can give it a name whatever you want, and I call it MTimeLibrary here.

2. Add your shared code into the project. I create a MTime class inherited from NSObject, which has a now method in the header file.

		@interface MTime : NSObject {
		}
		+ (void)now;
		@end

and here is the implement file:
 
	@implementation MTime
	+ (void)now
	{
	     NSLog(@"%@", []NSDate date]);
	} 
	@end 
	

* Compiled the project both in iPhone Simulator and iOS Device two modes, just Run Command + B. There will be a library named libMTimeLibrary.a under Product folder, right click to open it in the Finder.

* Open a terminal and combine the two static libaries. You can check the architecture of it by using a command:

 	  $ lipo -info libMtimeLibrary.a

In iPhone Simulator mode:

	$ lipo -info libMtimeLibrary.a
	input file libMtimeLibrary.a is not a fat file
	Non-fat file: libMtimeLibrary.a is architecture: i386
 
In iOS Device mode:

	$ lipo -info libMtimeLibrary.a
	input file libMtimeLibrary.a is not a fat file
	Non-fat file: libMtimeLibrary.a is architecture: armv7

Ok, We found that the library for each mode only can be used in their specify architecture. But how to make it to be available for all modes? There is a solution by combining the two static library.

	$ lipo -create "../Debug-iphonesimulator/libMtimeLibrary.a" \
	"../Debug-iphoneos/libMtimeLibrary.a" output "~/Desktop/libMTimeLibrary.a"

Now you can check the architecture of the output file by using 'lipo -info'

	$ lipo -info libMtimeLibrary.a
	Architectures in the fat file: libMtimeLibrary.a are: i386 armv7

Ok, this is just what we want.

* import the combined library and dependencies to the project. For above, you need add 
MTime.h, MTime.m, and the output library libMTimeLibrary.a to the project, then you can directly call **[MTime now]** after importing the header file MTime.h.
		

That's all.
