---
layout: post
title: MySQL和MongoDB基本用法对比
category: Programming
tags: [MySQL, MongoDB]
---

	Mongodb Dynamic querys select
	MySQL:
	    SELECT * FROM user
	Mongo:    
	    db.user.find()
 
 
	MySQL:
	    SELECT * FROM user WHERE name = 'foobar'
	Mongo:    
	    db.user.find({'name' : 'foobar'})
 
 
	MySQL:
	    INSERT INOT user (`name`, `age`) values ('foobar',25)
	Mongo:    
	   db.user.insert({'name' : 'foobar', 'age' : 25})
 
 
	if you want add a  column `email` on MySQL,you must :
	   ALTER TABLE user….
	But in Mongo,you can just:    
	  db.user.insert({'name' : 'foobar', 'age' : 25, 'email' : 'foo@bar.com'})
 
 
	MySQL:
	    DELETE * FROM user
	Mongo:    
	    db.user.remove({})
 
 
	MySQL:
	    DELETE FROM user WHERE age < 30
	Mongo:    
	    db.user.remove({'age' : {$lt : 30}})
 
	$gt : > ; $gte : >= ; $lt : < ; $lte : <= ; $ne : !=
 
 
	MySQL:
	    UPDATE user SET `age` = 36 WHERE `name` = 'foobar'
	Mongo:    
	    db.user.update({'name' : 'foobar'}, {$set : {'age' : 36}})
 
 
	MySQL:
	    UPDATE user SET `age` = `age` + 3 WHERE `name` = 'foobar'
	Mongo:    
	   db.user.update({'name' : 'foobar'}, {$inc : {'age' : 3}})
 
	        See more @ http://www.mongodb.org/display/DOCS/Updating
 
 
	MySQL:
	    SELECT COUNT(*) FROM user WHERE `name` = 'foobar'
	Mongo:    
	    db.user.find({'name' : 'foobar'}).count() 或 db.user.count({name:'foobar'});
 
 
	MySQL:
	    SELECT * FROM user limit 10,20
	Mongo:    
	   db.user.find().skip(10).limit(20)
 
 
	MySQL:
	    SELECT * FROM user WHERE `age` IN (25, 35,45)
	Mongo:    
	    db.user.find({'age' : {$in : [25, 35, 45]}})
 
 
	MySQL:
	    SELECT * FROM user ORDER BY age DESC
	Mongo:    
	    db.user.find().sort({'age' : -1})
 
 
	MySQL:
	    SELECT DISTINCT(name) FROM user WHERE age > 20
	Mongo:    
	   db.user.distinct('name', {'age': {$lt : 20}})
 
 
	MySQL:
	    SELECT name, sum(marks) FROM user GROUP BY name
	Mongo:    
	    db.user.group({
	        key : {'name' : true},
	        cond: {'name' : 'foo'},
	        reduce: function(obj,prev) { prev.msum += obj.marks; },
	        initial: {msum : 0}
	    });
 
 
	MySQL:
	    SELECT name FROM user WHERE age < 20
	Mongo:    
	   db.user.find('this.age < 20', {name : 1})
