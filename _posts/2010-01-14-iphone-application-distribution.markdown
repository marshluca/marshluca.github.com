---
layout: post
title: 提交iPhone应用到App Store
category: iOS
tags: [APNS]
---


今天第二次提交iPhone App, 流程比较多, 记录一下, 以免下次又花上大半天时间:

一.准备工作

1.登陆 iPhone Dev center: http://developer.apple.com/iphone/index.action

2.选择右上方得iPhone Developer Program Portal (需要购买开发证书)

3.如果还没有获取Distribusion证书，需要选择Certificates申请证书，并下载到本机, 提交后需要刷新才能看到

4.创建App ID，Bundle identifier, 注意格式: "com.domainname.appname"

5.选择Provisioning的distribusion, 不是develoment, 为当前app创建distribusion profile, 提交后同样需要刷新

6.下载刚提交的Distribusion profile到本地

7.点击mobileprovisioning文件即可安装到Xcode


二. 修改项目

1.找到App的info.plist，修改Bundle identifier为注册 App ID填写的Bundle identifier.

2.选中App > Get info, 在Configurations里选择release,然后duplicate，并命名为Distribusion

3.选中Build栏，然后Configuration栏选为Distribusion，Any iphone OS Device选为Distribusion下的iPhone
 Distribusion.

4.最后一步， 选择App的运行环境为Device + Distribusion, 编译！在App的目录/build/distribusion-iphoneos/下有一个.app，压缩就是要上传的binary.
