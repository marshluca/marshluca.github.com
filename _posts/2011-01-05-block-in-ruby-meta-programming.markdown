---
title: Ruby元编程之代码块
layout: post
category: Tech
tags: [Ruby, 元编程]
---

1.通过Kernel#block_given?判断是否跟有block, 通过yield关键字回调block, 可以给yield传递参数. 

	def a_method(a, b)
	  a + yield(a, b)
	end

	a_method(1, 2) {|x, y| (x + y) * 3 }  # => 10
    a_method(1, 2) # LocalJumpError: no block given (yield)	

	def a_method
	  return yield if block_given?
	  'no block'
	end

	a_method                          # => "no block"
	a_method { "here's a block!" }    # => "here's a block!"

    # 块之外的变量x对block是不可见的, 即闭包closure
	def my_method
	  x = "Goodbye"
	  yield("cruel")
	end

	x = "Hello"
	my_method {|y| "#{x}, #{y} world" } # => "Hello, cruel world"

2.作用域scope的切换是通过Kernel#local_variables动态跟踪绑定的名字. 

* 当程序进入类或模块或方法的定义, 就会发生作用域切换, 边界用class, def , module为标志. 
* class/module与def的微妙区别在于类和模块中的代码会被立即执行, 而方法中的代码只有在被调用时执行.

	    v1 = 1                  

		class MyClass
		  v2 = 2                
		  local_variables    # => [:v2]

		  def my_method
		    v3 = 3
		    local_variables
		  end

		  local_variables    # => [:v2]
		end

		obj = MyClass.new
		obj.my_method        # => [:v3]
		obj.my_method        # => [:v3]
		local_variables      # => [:v1, :obj]


3.通过 Class.new, Module.new, Module#define_method 代替 class, module, def 构造扁平化作用域

	my_var = "Success"

	MyClass = Class.new do
	  puts "#{my_var} in the class definition!"

	  define_method :my_method do
	    puts "#{my_var} in the method!"
	  end
	end

	MyClass.new.my_method     # => Success in the method!
		

	def define_methods
	  shared = 0
  
	  Kernel.send :define_method, :counter do
	    shared
	  end

	  Kernel.send :define_method, :inc do |x|
	    shared += x
	  end
	end

	define_methods

	counter       # => 0
	inc(4)
	counter       # => 4

4.通过Object#instance_eval, 在一个对象的上下文中执行一个block

	class MyClass
	  def initialize
	    @v = 1
	  end
	end

	obj = MyClass.new
	obj.instance_eval do
	  self        # => #<MyClass:0x3340dc @v=1>
	  @v          # => 1
	end

	v = 2
	obj.instance_eval { @v = v }
	obj.instance_eval { @v }      # => 2
 
5.使用 Proc#call 延迟执行 proc, lambda 

	dec = lambda {|x| x + 1 }
	dec.class # => Proc
	dec.call(2) # => 3

	inc = Proc.new {|x| x + 1 }
	# more code...
	inc.class   # => Proc
	inc.call(2) # => 3

	p = proc {|x| x + 1 }
	p.class # => Proc
	p.call(2) # => 3

6.通过 & 在 proc 与 block 间进行转换 

	def my_method(greeting)
	  puts "#{greeting}, #{yield}!"
	end

	my_proc = proc { "Bill" }
	my_method("Hello", &my_proc)
	
7.将 proc 作为参数传给一个方法	

	def math(a, b)
	  yield(a, b)
	end

	def teach_math(a, b, &operation)
	  puts "Let's do the math:"
	  puts math(a, b, &operation)
	end

	teach_math(2, 3) {|x, y| x * y}

	def my_method(&the_proc)
	  the_proc
	end

	p = my_method {|name| "Hello, #{name}!" }
	puts p.class
	puts p.call("Bill")

8.proc 与 lambda 的区别

    # 1. return 关键字的区别
 	def double(callable_object)
	  callable_object.call * 2
	end  

	l = lambda { return 10 }
	double(l) # => 20

	p = Proc.new { return 10 }
	# This fails with a LocalJumpError:
	# double(p)    

	p = Proc.new { 10 }
	double(p)   # => 20

    # 2. 参数要求上的区别
	p = Proc.new {|a, b| [a, b]}
	p.arity # => 2

	p = Proc.new {|a, b| [a, b]}
	p.call(1, 2, 3)   # => [1, 2]
	p.call(1)         # => [1, nil]
	
	l = lambda {|a, b| [a, b]}
	l.call(1, 2, 3)   # ArgumentError: wrong number of arguments (3 for 2)
	l.call(1, 2)      # => [1, 2]
	