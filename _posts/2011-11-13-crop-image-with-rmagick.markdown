---
title: Ruby中使用RMgick裁剪图片
layout: post
category: Tech
tags: [Ruby, RMgick]
---


	require "rubygems"
	require "RMagick"
	
	class ProcessPic
  
	  # 图片的风格 －－ 就是会根据不同的风格来缩放图片
	  STYLE_HASH = {
	    'default' => {'width' => 0, 'height' => 0},
	    'hdpi' => {'width' => 120, 'height' => 200},
	    'mdpi' => {'width' => 80, 'height' => 120},
	    'hdpi' => {'width' => 60, 'height' => 80},
	  }
	  # 缩放、裁剪图片
	  # 功能范例:http://www.weekface.info/2010/01/30/ruby-rmagic
	  #
	  # require 'rubygems'
	  # require 'RMagick'
	  # img =  Magick::Image.read('image/rails.png').first
	  # width, height = 100, 100
	  # thumb = img.resize(width, height) 
	  # thumb.write('image/rails_thumb.png')
	  #
	  def self.crop_resized(img, style)
	    width = STYLE_HASH[style]['width']
	    height = STYLE_HASH[style]['height']
	    thumb = img.resize(width, height)
 
	    thumb
	  end
  
	  # 功  能：被外部调用来把图片生成缩略图
	  # 参  数：fpic 是来源图片的名字，
	  #         fpath 是来源的路径
	  #         out_name 输出的文件名字（不包含扩展名）
	  #         out_path 输出的文件路径
	  #
	  def self.crop_thumb(fpic, fpath, out_name, out_path)
	    # 这里来保证不会覆盖原来的文件, 后面就不再判断了
	    unless File.exist?(fpath+"/"+fpic)
	      return nil
	    end
	    begin
	      img = Magick::Image.read(fpath+"/"+fpic).first
	      pext = fpic.match(/\.(\w+)$/)
	      pext = [''] if pext.blank?
 
	      Pic::STYLE_HASH.each do |k, v|
	        fpic_new = "#{out_name}_#{k}" + pext[0]
	        # 'default'的就是不再对图片做大小的处理
	        if (k != 'default')
	          tb = Pic.crop_resized(img, k)
	        else 
	          tb = img
	        end
	        tb.write(out_path + "/" + fpic_new)
	        # 写文件是否还需要额外的判断哪？似乎不需要的，
	        # 如果失败就是报错了，也就XXOO了
	        FileUtils.chmod 0755, (out_path + "/" + fpic_new)
	      end
	      # 不需要返回什么具体的数据
	      # fpic_new
	      true # 用之标识成功
	    rescue
	      nil
	    end
	  end
  
	end
 
	#ProcessPic.crop_thumb("land1.jpg","/data/www/eoemarket/upload/themes/000/000/059/59/Preview/", 'out_name', "/data/www/eoemarket/upload/themes/000/000/059/59/Preview/")
	#/upload/themes/000/000/059/59/Preview/
 
	ProcessPic.crop_thumb("marshluca.jpg", "~/Download/", "zhanglin", "~/Document")