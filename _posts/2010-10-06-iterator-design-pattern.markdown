---
title: 设计模式之Iterator
layout: post
category: Tech
tags: [设计模式, Ruby]
---

迭代器模式的百度百科解释:

> 提供一种方法顺序访问一个聚合对象中各个元素，而又不需暴露该对象的内部表示


Ruby 中可以利用 Enumerable 模块实现each方法:

 
	class Account
	  attr_accessor :name, :balance
  
	  def initialize(name, balance)
	    @name = name
	    @balance = balance
	  end
  
	  def <=>(other)
	    balance <=> other.balance
	  end
	end

	class Portfolio
	  include Enumerable
  
	  def initialize
	    @accounts = []
	  end
  
	  def each(&block)
	    @accounts.each(&block)
	  end
  
	  def add_account(account)
	    @accounts << account
	  end
	end

	lucy = Account.new("lucy", 10000)
	jack = Account.new("jack", 30000)
    portfolio = Portfolio.new
	portfolio.add_account(lucy)
	portfolio.add_account(jack)	
	portfolio.any? {|account| account.balance > 2000}