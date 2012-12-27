---
title: Ruby常用正则匹配
layout: post
category: Tech
tags: [Ruby, Regrexp]
---


	def gsub(str,re)
	  puts  str.gsub(re,"")
	end          
 
	def match(str,re)
	  if str =~ re
	    puts "#{$`}<#{$&}>#{$'}"
	  else
	    puts "no match"
	  end
	end   
 
	# remove html tags    
	gsub("<a href='www.google.com'>this is a link</a>Home<p clas='title'>Paragram</p>",/<(\S*?)[^>]*>.*?|<.*? \/>/)
 
	# remove space
	gsub("abcd\rcde\n\tda\s\ss\n",/\n\s*\r/)
              
	# check email
	match("marshluca@gmail.com",/\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*/)
                               
	# check url
	match("http://www.google.com",/[a-zA-z]+:\/\/[^\s]*/)
 
	# 匹配帐号是否合法(字母开头，允许5-16字节，允许字母数字下划线)
	match("_marshluca",/^[a-zA-Z][a-zA-Z0-9_]{4,15}$/)
                   
	# match the cell
	match("0714-4445853",/\d{3}-\d{8}|\d{4}-\d{7}/)
 
	# match qq acount
	match("346114585",/[1-9][0-9]{4,}/)
 
	# match zip code
	match("100081",/[1-9]\d{5}(?!\d)/)
 
	# match id
	match("42113218761817168",/\d{18}|\d{15}/)
                            
	# match ip address
	match("http://123.196.112.34",/\d+\.\d+\.\d+\.\d+/)