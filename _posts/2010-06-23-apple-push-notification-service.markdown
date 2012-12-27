--- 
layout: post
title: APNS证书问题
category: iOS
tags: [APNS, iOS]
---


关于Apple Push Notification Service的配置流程，以前在cocoachina上发过一篇详细的帖子：[http://www.cocoachina.com/bbs/read.php?tid-20723.html ](http://www.cocoachina.com/bbs/read.php?tid-20723.html )

今天再次在真机上测试，在获取device_token的时候碰到了这样的一个问题： 


>Error in registration. Error: Error Domain=NSCocoaErrorDomain Code=3000 UserInfo=0x1179f0 "未找到应用程序的“aps-environment”的权利字符串"  


刚开始以为是Provisioning Profiles出了问题，但是经过再三核查，确认的Push Notification状态已经是Enabled for Development. 

查看官方API文档,有这样一段描述： 

>5.Verify that the entitlements in the provisioning-profile file are correct. To do this, open the .mobileprovision file in a text editor. The contents of the file are structured in XML. In the Entitlements dictionary locate the aps-environment key. For a development provisioning profile, the string value of this key should be development; for a distribution provisioning profile, the string value should be production. 


查看mobileprovision文件，果然是缺少aps-environment这样的key-value. 


	<dict>  
	      <key>application-identifier</key>  
	      <string>R7W26X46PB.com.5yi.PushServer</string>  
	      <key>aps-environment</key>  
	      <string>development</string>  
	      <key>get-task-allow</key>  
	      <true/>  
	      <key>keychain-access-groups</key>  
	      <array>  
	              <string>R7W26X46PB.*</string>  
	      </array>  
	</dict>  

估计是下载mobileprovision文件的时候, APNS还没有激活. 重新在生成一次mobileprovision文件,再编译应该就可以了.
