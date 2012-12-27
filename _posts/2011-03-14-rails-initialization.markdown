---
title: Rails应用启动时的加载顺序
layout: post
category: Tech
tags: []
---


Rails2.3的时候, 启动应用后的加载顺序：
 

1. config/preinitializer.rb
2. config/environment.rb
3. config/environments/#{RAILS_ENV}.rb
4. plugin initialization
5. gem initialization
6. config/initializer/*.rb
7. all after_initialize blocks, in the order they were defined in (so same order as above)
8. any junk left below the Rails::Initializer.run call/block in environment.rb

在 Rails3 之后, 引入了Bundler，并且移除了preinitializer，加载顺序变成了
 
1. config/application.rb
2. gems initialization
3. config/environment.rb
4. config/environments/#{RAILS_ENV}.rb
5. plugin initialization
6. your rails app initialization
7. config/initializer/*.rb

如果想要在整个Rails项目之前加载代码需要写在config/application.rb中的require ‘rails/all’之前。
 

参考：
 
* [https://rails.lighthouseapp.com/projects/8994/tickets/4279-preinitializer-doesnt-preinitialize](https://rails.lighthouseapp.com/projects/8994/tickets/4279-preinitializer-doesnt-preinitialize)
* [https://github.com/rails/rails/commit/bd69589143371254b6d71aa74b1230144eb30a9c](https://github.com/rails/rails/commit/bd69589143371254b6d71aa74b1230144eb30a9c)