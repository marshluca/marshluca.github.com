---
title: Ruby元编程之方法
layout: post
category: Tech
tags: [Ruby, 元编程]
---


1.通过Object#send动态派发 (Dynamic Dispatch)

	class MyClass
	  def my_method(my_arg)
	    my_arg * 2
	  end
	end

	obj = MyClass.new
	obj.my_method(3)  # => 6

	obj.send(:my_method, 3)   # => 6
	
	1.send(:+, 2)   # => 3

2.使用Module#define_method动态定义方法

	class MyClass
	  define_method :my_method do |my_arg|
	    my_arg * 3
	  end
	end

	obj = MyClass.new
	obj.my_method(2)  # => 6

3.幽灵方法Kernel#method_missing

	class Lawyer
	  def method_missing(method, *args)
	    puts "You called: #{method}(#{args.join(', ')})"
	    puts "(You also passed it a block)" if block_given?
	  end
	end

	bob = Lawyer.new
	bob.talk_simple('a', 'b') do
	  # a block
	end
	

    # 简化版的OpenStruct构造方式
	class MyOpenStruct
	  def initialize
	    @attributes = {}
	  end

	  def method_missing(name, *args)
	    attribute = name.to_s
	    if attribute =~ /=$/
	      @attributes[attribute.chop] = args[0]
	    else
	      @attributes[attribute]
	    end 
	  end
	end

	icecream = MyOpenStruct.new
	icecream.flavor = "vanilla"
	icecream.flavor               # => "vanilla"

但是method_missing也容易引起错误方法调用: 如果在作用域之外使用一个局部变量, 会误以为调用一个未定义方法, 导致循环调用method_missing.

	class Roulette
	  def method_missing(name, *args)
	    person = name.to_s.capitalize
		# 通过 super 调用 Kernel#method_missing 避免死循环
	    super unless %w[Bob Frank Bill].include? person
	    number = 0
	    3.times do
	      number = rand(10) + 1
	      puts "#{number}..."
	    end
		# 作用域之外使用number
	    "#{person} got a #{number}"
	  end
	end

	number_of = Roulette.new
	puts number_of.bob
	puts number_of.frank

method_missing的性能测试

	class String
	  def method_missing(method, *args)
	    method == :ghost_reverse ? reverse : super
	  end
	end

	require 'benchmark'

	Benchmark.bm do |b|
	  b.report 'Normal method' do
	    1000000.times { "abc".reverse }
	  end
	  b.report 'Ghost method ' do
	    1000000.times { "abc".ghost_reverse }
	  end
	end

	        user     system      total      real
	Normal method   0.490000   0.000000   0.490000 (  0.494589)
	Ghost  method   1.020000   0.000000   1.020000 (  1.019200)

	
4.通过Module#undef_method, Module#remove_method移除已经存在的方法. undef_method 会删除所有的(包括继承)方法, remove_method只会删除接受者自己的方法.

	class Computer
   	  # 移除__|method_missing|respond_to之外的所有实例方法 (Blank Slate)
	  instance_methods.each do |m|
	    undef_method m unless m.to_s =~ /^__|method_missing|respond_to?/
	  end

	  def initialize(computer_id, data_source)
	    @id = computer_id
	    @data_source = data_source
	  end

	  def method_missing(name, *args)
	    super if !respond_to?(name)
	    info = @data_source.send("get_#{name}_info", args[0])
	    price = @data_source.send("get_#{name}_price", args[0])
	    result = "#{name.to_s.capitalize}: #{info} ($#{price})"
	    return "* #{result}" if price >= 100
	    result
	  end

	  def respond_to?(method)
	    @data_source.respond_to?("get_#{method}_info") || super
	  end
	end
	
	# 继承自BasicObject的类会自动成为白板类
	1.9.3p125 :204 > BasicObject.instance_methods
	 => [:==, :equal?, :!, :!=, :instance_eval, :instance_exec, :__send__, :__id__] 
