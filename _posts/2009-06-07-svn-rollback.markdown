---
layout: post
title: SVN Rollback
category: Tech
tags: [svn]
---



svn下有多种办法可以进行撤销修改。一般推荐用svn merge来解决。 

典型的操作过程如下： 

1、保证拿到的是最新代码： 

    svn update 

   假设是28版本。 
 
2、然后找出要移除的确切版本： 
  
    svn log user.rb 
  
 根据log怀疑是27版本改坏的，比较一下： 

    svn diff -r 26:27 user.rb

 发现果真是27版本坏事。 
 
3、撤销27版本的改动： 
   
    svn merge -r 27:26 user.rb

 为了保险起见，再次确认合并的结果： 
 
    svn diff user.rb
 
 发现已正确撤销了改动，提交。 
 
4、提交改动 
     
	svn commit -m "Revert wrong change from r27" 
	
 提交后版本变成了29。