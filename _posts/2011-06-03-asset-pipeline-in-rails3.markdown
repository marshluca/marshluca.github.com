---
title: Rails3.1中的Asset Pipeline
layout: post
category: Tech
tags: [Rails, Asset Pipeline]
---

Asset Pipeline 是 Rails 3.1 引入的重大更新。它提供一个框架,整合并压缩所有的js和css等资源,支持用CoffeeScript,Sass或Erb扩展编写.

* 为页面整合所有assets资源.在生产环境此特点极为重要,可以简少浏览器加载页面时对资源的请求次数.
* 压缩资源文件.比如css文件会移除所有的无用空白和注释, js则可以进行更复杂的压缩(如变量名可以优化等).可以选择不同的压缩机制或者自己进行定制.
* 用其他语言扩展资源文件内容.可以使用sass编写css,coffeeScript编写js,以及erb编写css或js,这样更好的集成rails的helper.

在rails3.1以前,一般要通过第三方库比如jammit和sprockets来实现assets机制,而rails3.1默认的actionpack已支持, 主要通过sprockets插件实现. Rails本身也可以通过helper来引用资源文件,如”javascipt_include_tag”或”stylesheet_link_tag”加上”:cache=> true”, 实现资源缓存到浏览器上.不过存在局限性,比如,它不能提前缓存资源,不能隐式包含一些第三方库的资源.

Rails3.1默认把所有的js和css文件整合到一个文件中.当然,你也可以按实际需要差分成多个文件.在生产环境,每个生成的资源文件会追加一个MD5的数字摘要串.浏览器会直接缓存这个文件,而当MD5串在后台环境修改了,浏览器会重新请求缓存.

参考:
[http://guides.rubyonrails.org/asset_pipeline.html](http://guides.rubyonrails.org/asset_pipeline.html)
