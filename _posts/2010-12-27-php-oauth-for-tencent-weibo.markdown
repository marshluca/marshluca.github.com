---
layout: post
title: 腾讯微博PHP OAuth认证[更新]
category: Tech
tags: [PHP, OAuth]
---


之前研究过新浪微博OAuth授权，刚巧腾讯微博最近也开放API了. 

本想使用Ruby OAuth来进行授权，只是每次在获取AcessToken的时候，都出现签名错误。
问题耽搁了很久，且一直未能解决，最后是直接使用了官方的PHP SDK来绕过了，并结合需求，实现了两个功能：

1. 授权成功的同时，绑定到自家网站的用户id,没有就生成一个.
2. 将PHP的发微博功能封装成了一个API，供自家调用.

<script src="https://gist.github.com/831365.js"> </script>


**2011-6-17更新:**

之前的Ruby OAuth认证有了新的进展, nonce的签名错误已经解决, 下面是完整的demo:

<script src="https://gist.github.com/1031385.js"></script>
	