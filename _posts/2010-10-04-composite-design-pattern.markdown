---
title: 设计模式之Composite
layout: post
category: Tech
tags: [设计模式, Ruby]
---

组合模式的百度百科解释:

> 将对象组合成树形结构以表示“部分整体”的层次结构。组合模式使得用户对单个对象和使用具有一致性。

组合模式解耦了客户程序与复杂元素内部结构，从而使客户程序可以向处理简单元素一样来处理复杂元素。如果你想要创建层次结构，并可以在其中以相同的方式对待所有元素，那么组合模式就是最理想的选择。

**Ruby中的实现**

	class Task
	  attr_reader :name
	  
	  def initialize(name)
	    @name = name
	  end
	  
	  def time_required
	    0.0
	  end
	end
	
	class AddDryIngredientsTask < Task
	  def initialize
	    super("Add Dry Ingredients.")
	  end
	  
	  def time_required
	    1.0
	  end
	end
	
	class AddLiquidsTask < Task
	  def initialize
	    super("Add Liquids.")
	  end
	  
	  def time_required
	    1.0
	  end
	end
	
	class MixTask < Task
	  def initialize
	    super("Mix up.")
	  end
	  
	  def time_required
	    3.0
	  end
	end
	
	class CompositeTask < Task
	  def initialize(name)
	    super(name)
	    @sub_tasks = []
	  end
	  
	  def add_sub_task(task)
	    @sub_tasks << task
	  end
	  
	  def time_required
	    time = 0.0
	    @sub_tasks.each { |t| time += t.time_required }
	    return time
	  end
	end
	
	class MakeBatterTask < CompositeTask
	  def initialize
	    super("Make Batter")
	    add_sub_task(AddDryIngredientsTask.new)
	    add_sub_task(AddLiquidsTask.new)
	    add_sub_task(MixTask.new)
	  end
	end
	
	class MakeCakeTask < CompositeTask
	  def initialize
	    super("Make Cake")
	    add_sub_task(MakeBatterTask.new)
	  end
	end
	
	task = MakeCakeTask.new
	puts task.time_required
