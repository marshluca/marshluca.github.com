---
layout: post
title: MongoDB服务的启动和停止
category: Database
tags: [MongoDB]
---


1.mongodb启动方式：

	sudo mongod --dbpath /mnt/mongodb/data/ --logpath /mnt/mongodb/log/mongod.log --fork  --logappend

2.mongodb 停止方式：

	$ ./mongo
	> db.shutdownServer()

3.如果mongodb的停止是因为进程不小心被kill掉的话，启动的时候需要先repaire:

    # 1.unlock  
	sudo mv /mnt/mongodb/data/mongod.lock /mnt/mongodb/data/mongod.lock.old       	   
    # 2.repaire  
    sudo mongod --repair --dbpath /mnt/mongodb/data/	   
    # 3.start  
    sudo mongod --dbpath /mnt/mongodb/data/ --logpath /mnt/mongodb/log/mongod.log --fork  --logappend 
	   

参考：<a href="http://www.mongodb.org/display/DOCS/Durability+and+Repair">http://www.mongodb.org/display/DOCS/Durability+and+Repair</a>
