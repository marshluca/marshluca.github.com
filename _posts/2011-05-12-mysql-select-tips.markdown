---
title: MySQL的Select技巧
layout: post
category: Tech
tags: [MySQL]
---

1、select语句可以用回车分隔
 
    select * from article
    where id=1;
	
2、批量查询数据可以用in来实现
 
    select * from article where id in(1,3,5);
 
3、使用concat连接查询的结果

    select concat(id,"-",con) as res from article where id=1;
    => "1-article content"
 
4、使用locate, 不存在返回0

    select locate("hello","hello baby");
	=> 1

5、使用group by. 经常与AVG(),MIN(),MAX(),SUM(),COUNT()一起使用

    # 从customer表里列出所有不重复的城市，及其数量（有点类似distinct）
    select city, count(*) from customer group by city;
 
6、使用having, 允许有条件地聚合数据为组

    # 先按city归组，然后找出city地数量大于10的城市
	# 使用group by + having 速度有点慢
    select city,count(*),min(birth_day) from customer 
	group by city 
	having count(*)>10;
 
7、组合子句: where、group by、having、order by（如果这四个都要使用的话，一般按这个顺序排列）
 
8、使用distinct

    select distinct city from customer order by id desc;
	
9、使用limit

    select * from article limit 100,-1;
 
10、多表查询

	select user_name from user  u,member  m
	where u.id=m.id and 
	m.reg_date>=2006-12-28
	order by u.id desc;
	