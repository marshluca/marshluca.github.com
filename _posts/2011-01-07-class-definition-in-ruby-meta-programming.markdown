---
title: Ruby元编程之类定义
layout: post
category: Tech
tags: [Ruby, 元编程]
---


1.使用 Module#class_eval 打开一个类

* class_eval 会同时修改 self 和当前类, 而instance_eval 仅仅会修改 self 
* class_eval 与 class 关键字相比, 使用的是扁平作用域

		def add_method_to(a_class)
		  a_class.class_eval do
		    def m; 'Hello!'; end
		  end
		end

		add_method_to String
		"abc".m   # => "Hello!"

2.类实例变量, 类的实例对象的实例变量, 类变量

	class MyClass
	  @my_var = 1
	end

	class MyClass
	  @my_var = 1

	  def self.read; @my_var; end
	  def write; @my_var = 2; end
	  def read; @my_var; end
	end

	obj = MyClass.new
	obj.write
	obj.read            # => 2
	MyClass.read        # => 1

    # 类变量
	class C
	  @@v = 1  
	end

	class D < C
	  def my_method; @@v; end
	end

	D.new.my_method  # => 1

	@@v = 1

	class MyClass
	  @@v = 2
	end
  
    # 访问外部类变量
	@@v  # => 2

3.通过 eigenclass 定义类属性, 类方法

	class MyClass
	  class << self
	    attr_accessor :c
		
		def hello
		 "Hello"
		end
	  end
	end

	MyClass.c = 'It works!'
	MyClass.c               # => "It works!"
	MyClass.hello           # => "Hello"


4.通过 Object#extend 和 include 来扩展类

	module MyModule
	  def my_method; 'hello'; end
	end

	obj = Object.new
	obj.extend MyModule
	obj.my_method       # => "hello"

	class MyClass
	  extend MyModule
	end
	MyClass.my_method   # => "hello"

	module MyModule
	  def my_method; 'hello'; end
	end

	class MyClass
	  class << self
	    include MyModule
	  end
	end

	MyClass.my_method   # => "hello"

5.alias 环绕别名

	class String
	  # 先定义别名
	  alias :real_length :length
  
      # 重定义length
	  def length
	    real_length > 5 ? 'long' : 'short'
	  end
	end

	"War and Peace".length      # => "long"
	"War and Peace".real_length # => 13


	class Fixnum
	  alias :old_plus :+
  
	  def +(value)
	    self.old_plus(value).old_plus(1)
	  end
	end
	
	1 + 1    # => 3
	1 - 1	 # => 0	

6.动态转发实现 deprecate 废弃方法

	class Book
	  def title # ...
	  end

	  def subtitle # ...
	  end
  
	  def lend_to(user)
	    puts "Lending to #{user}"
	    # ...
	  end

	  def self.deprecate(old_method, new_method)
	    define_method(old_method) do |*args, &block|
	      warn "Warning: #{old_method}() is deprecated. Use #{new_method}()."
	      send(new_method, *args, &block)
	    end
	  end

	  deprecate :GetTitle, :title
	  deprecate :LEND_TO_USER, :lend_to
	  deprecate :title2, :subtitle
	end

	b = Book.new
	b.LEND_TO_USER("Bill")
	# >> Warning: LEND_TO_USER() is deprecated. Use lend_to().
	# >> Lending to Bill
