---
layout: post
title: MySQL 常用命令
category: Life
tags: [MySQL]
---


1.增加主键
	alter table tabelname add new_field_id int(5) unsigned default 0 not null auto_increment ,add primary key (new_field_id);

2.增加一个新列
	alter table t2 add d timestamp;
	alter table infos add ex tinyint not null default '0';

3.删除列
	alter table t2 drop column c;

4.重命名列
	alter table t1 change a b integer;

5.改变列的类型
	alter table t1 change b b bigint not null;
	alter table infos change list list tinyint not null default '0';

6.重命名表
	alter table t1 rename t2;

7.加索引
	alter table tablename change depno depno int(5) not null;
	alter table tablename add index索引名(字段名1[，字段名2 …]);
	alter table tablename add index emp_name (name);

8.加主关键字的索引
	alter table tablename add primary key(id);

9.加唯一限制条件的索引
	alter table tablename add unique emp_name2(cardnumber);

10.删除某个索引
	alter table tablename drop index emp_name;


11.增加字段：
	ALTER TABLE table_name ADD field_name field_type;

12.修改原字段名称及类型：
	ALTER TABLE table_name CHANGE old_field_name new_field_name field_type;

13.删除字段：
	ALTER TABLE table_name DROP field_name;
