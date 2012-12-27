---
title: 设计模式之Command
layout: post
category: Tech
tags: [设计模式, Ruby]
---

百度百科中对命令模式的解释:

> 在软件系统中，“行为请求者”与“行为实现者”通常呈现一种“紧耦合”。但在某些场合，比如要对行为进行“记录、撤销/重做、事务”等处理，这种无法抵御变化的紧耦合是不合适的。在这种情况下，如何将“行为请求者”与“行为实现者”解耦？将一组行为抽象为对象，实现二者之间的松耦合。这就是命令模式（Command Pattern）


命令模式的本质是对命令进行封装，将发出命令的责任和执行命令的责任分割开, 命令本身也是一个对象, 这个对象和其他对象一样可以被存储和传递。


	class Command
	  attr_reader :description
	  
	  def initialize(description)
	    @description = description
	  end
	  
	  def execute
	  end
	end
	
	class CreateFile < Command
	  def initialize(file_name)
	    super("create file: #{file_name}")
	  end
	  
	  def execute
	    puts "creating file"
	  end
	  
	  def unexecute
	    puts "deleting created file"
	  end
	end
	
	class CopyFile < Command
	  def initialize(file_name)
	    super("copy file: #{file_name}")
	  end
	  
	  def execute
	    puts "copying file"
	  end
	  
	  def unexecute
	    puts "deleting copied file"
	  end
	end
	
	class CompositeCommand < Command
	  def initialize
	    @commands = []
	  end
	  
	  def add_command(cmd)
	    @commands << cmd
	  end
	  
	  def execute
	    @commands.each { |cmd| cmd.execute }
	  end
	  
	  def unexecute
	    @commands.reverse.each { |cmd| cmd.unexecute }
	  end
	  
	  def description
	    description = ""
	    @commands.each { |cmd| description += cmd.description + "\n" }
	    description
	  end
	end


	cmds = CompositeCommand.new	
	cmds.add_command(CreateFile.new("file"))
	cmds.add_command(CopyFile.new("file"))	
	cmds.execute
	cmds.unexecute


