---
layout: post
title: "Rails中乱码解决方案"
category: iOS
tags: [Rails]
---




针对 ruby on rails 开发中碰到的乱码问题, 整理了一下网上的解决方案:

1.确定MySQL数据库编码是utf8 

2.config/database.yml里面增加encoding: utf8 

3.确定rhtml文件编码是UTF-8 

4.设置ApplicationController的header里charset

	class ApplicationController < ActionController::Base 
	  before_filter :configure_charsets 
	  def configure_charsets 
	    @response.headers["Content-Type"] = "text/html; charset=utf-8" 
	    suppress(ActiveRecord::StatementInvalid) do 
	      ActiveRecord::Base.connection.execute 'SET NAMES utf8' 
	    end 
	  end 
	end 

5.针对迁移migration加UTF-8编码

	class CreateUsers < ActiveRecord::Migration 
	  def self.up 
	    create_table :users, : options => 'CHARSET=utf8' do |t| 
	      t.column :name, :string 
	      t.column :password, :string 
	      t.column :birthday, :datetime 
	      t.column :email, :string 
	      t.column :address, :text 
	    end 
	  end 

	  def self.down 
	    drop_table :users 
	  end 
	end
