---
layout: post
title: 升级rubygems
category: Tech
tags: [Ubuntu, gem]
---


最近在Ubuntu是安装一些gems的时候, 很多都提示 rubygems 版本过低, 要求升级, 于是就试着更新了下.

    $ gem -v
    1.2.0
    $ gem update --system
    ERROR: While executing gem ... (RuntimeError)
    gem update --system is disabled on Debian. RubyGems can be updated \
    using the official Debian repositories by aptitude or apt-get

提示说不能通过 gem update --system 方式更新, 就试了试手动安装方式, 发现也挺好使的, 很安全 :-)

    $ wget http://rubyforge.org/frs/download.php/45905/rubygems-1.3.1.tgz
    $ tar xzf rubygems-1.3.1.tgz
    $ cd rubygems-1.3.1
    $ sudo ruby setup.rb
    $ gem -v
    1.3.1
	
