---
layout: post
title: "Ubuntu网络配置"
category: Unix
tags: [Ubuntu]
---


1.靜态IP配置

    # sudo gedit /etc/network/interfaces
	auto eth0
	iface eth0 inet static
	address xxx.xxx.xxx.xxx #ip地址
	netmask 255.255.255.0 #子网掩码
	gateway xxx.xxx.xxx.xxx #网关

2.配置DNS

    # sudo gedit /etc/resolv.conf
    nameserver xxx.xxx.xxx.xxx
    nameserver xxx.xxx.xxx.xxx #备用DNS

