---
title: SSH登陆服务器免输密码
layout: post
category: Tech
tags: [SSH, Git, Linux]
---

用Git对公司项目代码进行版本控制的时候, 由于走的是SSH通讯协议, 每次进行git push或git pull, 服务器都要询问输入密码. 如果密码稍微复杂点, 那开发就苦不堪言了. 

下面有一种办法, 是将个人SSH公钥放到服务器的 ~/.ssh/authorized_keys 中, 以后每次更新代码就可以免输密码了.

	scp ~/.ssh/id_rsa.pub sdkadmin@118.26.224.37:marshluca_id_rsa.pub

	cat marshluca_id_rsa.pub >> ~/.ssh/authorized_keys
	
PS: 这是Linux上的解决方案, Windows 暂时还未试验过.	
	