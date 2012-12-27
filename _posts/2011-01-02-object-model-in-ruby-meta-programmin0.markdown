---
title: Ruby元编程之对象模型
layout: post
category: Tech
tags: [Ruby, 元编程]
---


1.Open Class: 总是可以重新打开已经存在的类, 并进行动态修改, 即使是String, Array这样的Ruby标准库中的类. 但是需要确保没有覆盖已有的方法定义.

	class D
	  def x; 'x'; end
	end

	class D
	  def y; 'y'; end
	end

	obj = D.new
	obj.x        # => "x"
	obj.y        # => "y"

	class String
	  def to_alphanumeric
	    gsub /[^\w\s]/, ''
	  end
	end

2.一个对象的实例变量存在于对象本身, 而一个对象的方法存在于对象自身的类. 这也解释了为什么同一个类的对象共享实例方法而不共享实例变量.

	class MyClass
	  def my_method
	    @v = 1
	  end
	end

	obj = MyClass.new
	obj.class           # => MyClass

	obj.my_method
	obj.instance_variables  # => [:@v]

	obj.methods.grep(/my/)  # => [:my_method]

	String.instance_methods == "abc".methods    # => true
	String.methods == "abc".methods             # => false
	

3.Ruby 1.9之后, 所有的类都继承自Object, Object继承自BasicObject.	BasicObject是Ruby对象体系中的根节点.

	1.8.7 :022 > String.superclass
	 => Object 
	1.8.7 :023 > Class.superclass
	 => Module 
	1.8.7 :024 > Module.superclass
	 => Object 
	1.8.7 :025 > Object.superclass
	 => nil 
	 
	1.9.3p125 :001 > String.superclass
	=> Object 
	1.9.3p125 :002 > Class.superclass
	=> Module 
	1.9.3p125 :003 > Module.superclass
	=> Object 
	1.9.3p125 :004 > Object.superclass
	=> BasicObject 	 	 
	1.9.3p125 :006 > BasicObject.superclass
	 => nil	
	
4.Class是一个增强的Module, 只增加了三个方法, 除此之外, Class和Module基本一样. 这就是为什么Class可以实例化, 而Module不能.

	inherited = false
	Class.instance_methods(inherited)  # => [:superclass, :allocate, :new]

5.instance_variable_get, instance_variable_set, instance_eval

	class MyClass
	  def my_method
	    @v = 1
	  end
	end

	obj = MyClass.new
	obj.my_method

	1.9.3p125 :041 > obj.methods.grep /instance/
	 => [:instance_variables, :instance_variable_get, :instance_variable_set, :instance_variable_defined?, :instance_of?, :instance_eval, :instance_exec] 
    1.9.3p125 :039 > obj.instance_variables
	 => [:@v]	 
 	1.9.3p125 :040 > obj.instance_variable_defined? :@v
 	 => true 	 
	1.9.3p125 :041 > obj.instance_variable_get :@v
	 => 1 
	1.9.3p125 :042 > obj.instance_variable_set :@v, 3
	 => 3 
	1.9.3p125 :043 > obj.instance_variable_get :@v
	 => 3 
	1.9.3p125 :044 > obj.instance_of? Class
	 => false 
	1.9.3p125 :045 > obj.instance_of? MyClass
	 => true 
	1.9.3p125 :046 > obj.instance_eval do 
	1.9.3p125 :047 >     @v = 5
	1.9.3p125 :048?> end
	 => 5 
	1.9.3p125 :049 > obj.instance_variable_get :@v
	 => 5 

6.方法查找:向右找到所在的类查找, 然后再向上沿着祖先链查找.

	class MyClass
	  def my_method; 'my_method()'; end
	end

	class MySubclass < MyClass
	end

	obj = MySubclass.new
	obj.my_method()       # => "my_method()"

	MySubclass.ancestors # => [MySubclass, MyClass, Object, Kernel, BasicObject]

    # 混有Module的类
	module M
	  def my_method
	    'M#my_method()'
	  end
	end
	
	module N
  	  def my_method
	    'N#my_method()'
	  end
	end

	class C
	  include M
	  include N
	end

	class D < C; end

	D.new.my_method() # => "N#my_method()"

	D.ancestors # => [D, C, N, M, Object, Kernel, BasicObject]
   
7.Object类中include了Kernel模块, 所以像print, attr_reader, attr_writer, sleep, system, exit等类似的私有方法可以在代码中的任何地方调用.

	1.9.3p125 :061 > Kernel.class
	 => Module 
	1.9.3p125 :062 > Module.private_instance_methods
	 => [:included, :extended, :method_added, :method_removed, :method_undefined, :initialize_copy, :attr, :attr_reader, :attr_writer, :attr_accessor, :initialize, :remove_const, :append_features, :extend_object, :include, :remove_method, :undef_method, :alias_method, :public, :protected, :private, :module_function, :define_method, :default_src_encoding, :irb_binding, :remove_instance_variable, :sprintf, :format, :Integer, :Float, :String, :Array, :warn, :raise, :fail, :global_variables, :__method__, :__callee__, :eval, :local_variables, :iterator?, :block_given?, :catch, :throw, :loop, :caller, :trace_var, :untrace_var, :at_exit, :syscall, :open, :printf, :print, :putc, :puts, :gets, :readline, :select, :readlines, :`, :p, :test, :srand, :rand, :trap, :exec, :fork, :exit!, :system, :spawn, :sleep, :exit, :abort, :load, :require, :require_relative, :proc, :lambda, :binding, :set_trace_func, :Rational, :Complex, :gem, :gem_original_require, :singleton_method_added, :singleton_method_removed, :singleton_method_undefined, :method_missing]

8.在Ruby的顶级上下文中, slef是main对象, 而在类和模块定义中self是其本身

	self         # => main
	self.class   # => Object
  
	class MyClass
	  self        # => MyClass
	end
	 
	class MyClass
	  def testing_self
	    @var = 10     # An instance variable of self
	    my_method()   # Same as self.my_method()
	    self
	  end
  
	  def my_method
	    @var = @var + 1
	  end
	end

	obj = MyClass.new
	obj.testing_self  # => #<MyClass:0x510b44 @var=11>
	 