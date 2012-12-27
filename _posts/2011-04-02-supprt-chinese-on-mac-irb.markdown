---
title: Mac IRB环境下支持中文
layout: post
category: Tech
tags: [Mac, Ruby]
---


Mac的Terminal里面是可以直接输入中文的，但是在Ruby的irb里，却出现了无法输入中文问题.
查了一下, 是缺少 readline 支持, 由于是用 rvm 安装的 Ruby, 所以需要rvm重新编译安装一下Ruby, 刚好 rvm 支持 reconfigure :
 
	brew install readline
	brew link readline
	rvm --reconfigure --force -C --with-readline-dir=/usr/local install 1.8.7
 
	1.8.7 :001 > puts "你好, 世界!"
	你好, 世界!
	 => nil  