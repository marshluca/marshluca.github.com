---
layout: post
title: "Rails中使用file_column上传图片"
category: Ruby
tags: [Rails, gem]
---



file_column是一个比较好用的文件处理插件，结合RMagic可以实现图片的放大与缩小显示.
具体使用步骤如下：

1.安装file_column，直接把file_column的压缩包解压，放在vendor/plugins目录下面就可以了.

2.在数据库的表中需要有一个字段来存放文件名，以表users中的logo字段来示范

3.在model目录中，修改user.rb文件，增加如下内容：

	classs User < ActivieRecord::Base
	  validates_format_of :logo,  
	    :with=>/^.*(.jpg|.JPG|.gif|.GIF)$/,  
	    :message => "你只能上传JPG或则GIF的图片文件"

	  file_column :logo, :magick => {   
	    :versions => { "thumb" => "50x50", "medium" => "640x480>" }  
	  }  
	end

4、上传图片页面的view

	<%= file_column_field 'user', 'logo' %>

5、展示图片页面的view

	<% for user in @users %>
	  <%= image_tag url_for_file_column 'user', 'logo' %>
	<% end %>

这里url_for_file_column是file_column提供的一个helper方法.
完成以上的步骤，就可以正常的上传/显示图片了。