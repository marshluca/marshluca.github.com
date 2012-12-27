---
title: MySQL连接数监测技巧
layout: post
category: Tech
tags: [MySQL]
---


1、查看当前所有连接的详细信息:

	> mysqladmin -uroot -p -h127.0.0.1 processlist
 
 
	+----+------+-----------------+----+---------+------+-------+------------------+
	| Id | User | Host            | db | Command | Time | State | Info             |
	+----+------+-----------------+----+---------+------+-------+------------------+
	| 3  | root | localhost:51604 |    | Query   | 0    |       | show processlist |
	+----+------+-----------------+----+---------+------+-------+------------------+
	
2、只查看当前连接数(Threads就是连接数):

	> mysqladmin  -uroot -p -h127.0.0.1 status
	
	Uptime: 332808  Threads: 1  Questions: 3  Slow queries: 0  Opens: 33  Flush tables: 1  Open tables: 26  Queries per second avg: 0.000