---
layout: post
title: Mac上搭建Android开发环境
category: iOS
tags: [Android, Mac]
---



1.JDK安装. 要求1.5以上, 一般的Mac中已经预装了jdk，我的Mac是10.6.3, 预装了jdk 1.6, 用"java -version"在terminal可以查看.

2.Eclipse IDE安装. 需要去eclipse官网下载，我下载的是eclipse 3.5.2.解压到合适的路径，不需要编译安装，直接运行目录下的eclipse.app文件就可以启动eclipse.

3.Android sdk安装. 到Android官网下载(我是翻墙过去的，下载的是android
 2.1). 同Eclipse一样，只需要解压到合适目录.同时需要将该目录下的tools路径加到PATH中.编辑~/.bash_profile, 添加一行：
 
    PATH=${PATH}:/{your-android-sdk}/tools.

4.安装Android Developer Tools. 打开Eclipse，点击Help菜单，找到Install New Sofeware，在Add Site中添加一个location为：http://dl-ssl.google.com/android/eclipse/，然后会出现Android DDMS,和Android Development Tools，选择并Install.

5.开启Terminal,使用"android list targets"发现没有可以sdk target.需要在eclipse的window菜单里面打开 Android SDK And AVD Manager窗口，找到avilable packages，选中2.1相关的packages.为了方便编译开源早期版本的代码.我全选了. 创建avd:

    android create avd --name sdk_2_1_version  --target 7

6.打开Eclipse，在preference里面配置android的home路径. 环境搭建完毕.

7.HelloWorld. 新建一个project,命名，选中target列表中的Android 2.1,对于的min sdk version是target的id号,可以Finish了.

8.运行emulator.我运行的时候遇到了个提示：The project cannot be built until build path errors .表明相关的库没有引入.在project的properties里面有个order and export选项，选中android 2.1 Library就ok了.再次运行,应该可以看到emulator了.
