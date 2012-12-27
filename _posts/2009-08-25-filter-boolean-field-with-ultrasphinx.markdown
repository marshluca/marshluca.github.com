---
title: 使用Ultrasphinx查找布尔字段
layout: post
category: Tech
tags: [Rails, Ultrasphinx]
---


1. 在is_indexed的fields中加入需要过滤的boolean字段is_movie
2. 在ultrasphinx的hash参数中添加 :filters => {:is_movie=>1}

		require 'ultrasphinx'
		class Activity < ActiveRecord::Base
		  has_one :act_text
		  has_one :activity_name
 
		  is_indexed :fields=>['act_subject','start_time','end_time','act_location','category','is_movie'],
		    :conditions=>"activities.access_type = '公开' and activities.auth = 1",
		    :include    => [{:association_name => "act_text",
		      :field    => "act_description",
		      :as       => "act_description"},
		    :delta=>{:field=>"updated_on"}]	
	   
 
		  def self.search(keyword, city, page, is_movie, end_time)
		    query = "category:#{keyword} and act_location:#{city}"
		    search = Ultrasphinx::Search.new(
		      :class_names => "Activity",
		      :query => query,
		      :per_page => 10,
		      :page => page,
		      :filters=>{:is_movie => is_movie, :end_time => end_time}
			)
		    search.run
		    search.results, search.total_entries
		  end
		end
 
只要改变了索引字段, 就需要对sphinx重新做index, 并重启 searchd 服务:

	rake ultrasphinx:configure
	rake ultrasphinx:daemon:restart 