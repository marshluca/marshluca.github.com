---
layout: post
title: 读《应用Rails进行Web敏捷开发》
category: Ruby
tags: [Rails]
---

国庆节这几天, 在读 <应用Rails进行Web敏捷开发>, 实践的时候, 碰到了几个问题. 

Rails已经更新到2.1，而书编写时使用的Rails版本还是1.8.5, 自Rails 2.0后, 很多plugin都被剥离出来了，比如快速创建脚手架的scaffold.


下面记录一下几个问题的解决办法：

1.undefined method `scaffold' 问题, 需要安装scaffolding插件.

	ruby script/plugin install scaffolding

2.undefined method `paginate' 问题, 安装老的classic_pagination就可以了.

	ruby script/plugin install http://tools.assembla.com/svn/breakout/breakout/vendor/plugins/classic_pagination/  


很多的第三方plugin都可以在这里找到:

[http://agilewebdevelopment.com/plugins/](http://agilewebdevelopment.com/plugins/)