---
title: 设计模式之Observer
layout: post
category: Tech
tags: [设计模式, Ruby, Objective-C]
---

观察者模式的百度百科解释:

> 观察者<Observer>模式（有时又被称为发布-订阅<Publish/Subscribe>模式、模型-视图<Model/View>模式、源-收听者<Source/Listener>模式或从属者<Dependents>模式）是软件设计模式的一种。在此种模式中，一个目标物件管理所有相依于它的观察者物件，并且在它本身的状态改变时主动发出通知。

定义对象间的一种一对多的依赖关系, 以便当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并自动刷新。观察者模式在模块之间划定了清晰的界限，提高了应用程序的可维护性和重用性。

**1. Objective-C 中的实现**
 
iOS SDK里的NSNotificationCenter就是一个很好的观察者模式应用

TabBarController.h

	@interface TabBarController : UITabBarController

	- (void)showTabBar;
	- (void)hideTabBar;
		
	@end


TabBarController.m

    @implementation TabBarController

	- (void)viewDidLoad
	{
	    [super viewDidLoad];
    
	    // add obsevers
	    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(showTabBar) name:kShowTabBarNotification object:nil];
	    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(hideTabBar) name:kHideTabBarNotification object:nil];    
	}

	- (void)viewDidUnload
	{
	    [super viewDidUnload];
	    // Release any retained subviews of the main view.
    
	    // remove observers
	    [[NSNotificationCenter defaultCenter] removeObserver:self name:kShowTabBarNotification object:nil];
	    [[NSNotificationCenter defaultCenter] removeObserver:self name:kHideTabBarNotification object:nil];    
	}


	#pragma mark Observer Methods

	- (void)showTabBar
	{
	    NSLog(@"show tab bar");
	}

	- (void)hideTabBar
	{
	    NSLog(@"hide tab bar");
	}

    @end
	
如果需要隐藏或是展示tab bar的时候, 只需要发送一个通知就可以了
	
	[[NSNotificationCenter defaultCenter] postNotificationName:kHideTabBarNotification object:nil];
	
**2. Ruby 中的实现**

	module Observer
	  def initialize
	    @observers = []
	  end
	  
	  def add_observer(observer)
	    @observers << observer
	  end
	  
	  def delete_observer(observer)
	    @observers.delete(observer)
	  end
	  
	  def notify_observers
	    @observers.each { |observer| observer.update(self) }
	  end
	end
	
	class Employee
	  include Observer
	  attr_reader :name, :salary
	  
	  def initialize(name, salary)
	    super()
	    @name = name
	    @salary = salary
	  end
	  
	  def salary=(new_salary)
	    @salary = new_salary
	    notify_observers
	  end	  
	end
	
	class Payroll
	  def update(changed_employee)
	    puts("Cut a new check for #{changed_employee.name}!")
	    puts("His salary is now #{changed_employee.salary}!")
	  end
	end
	
	class TaxMan
	  def update( changed_employee )
	    puts("Send #{changed_employee.name} a new tax bill!")
	  end
	end
	
	fred = Employee.new('Fred', 30000)
	payroll = Payroll.new
	taxman = TaxMan.new
	fred.add_observer(payroll)
	fred.add_observer(taxman)	
	fred.salary = 35000.0
	
