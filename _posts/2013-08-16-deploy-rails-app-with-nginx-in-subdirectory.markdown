---
title: Deploy Rails app with Nginx in subdirectory
layout: post
category: Tech
tags: [Rails, Nginx]
---

Follow [my previous post](http://marshluca.com/2013/08/05/how-to-change-a-scheduled-job-in-sidekiq/), this article will try to figure out how to organize
Rails app and Wordpress with different subdirectory under the same
domain.

For Wordpress, what we need is put it under the root directory of Apache, then we can directly visit it at `example.com/blog`.

For Rails app, we use Unicorn as app server, Nginx as web server. When we deploy it with subdirectory, we need to care about two things.


### Make Rails routes work with subdirectory

* configure root directory in config/application.rb

      config.root_directory = '/app'

* configure Rails Routes under `app` scope in config/routes.rb
   
      require "rails/application"

      module RouteScope
        def self.root
          Rails.application.config.root_directory
        rescue NameError
          '/'
        end
      end
      
      RailsApp::Application.routes.draw do
        scope RouteScope.root do 
          root to: 'home#index'
          # other routes here ...
        end
      end

* configure Rails app with Nginx in `app` subdirectory

      upstream rails_app_server {
        server unix:/path/to/railsapp/tmp/sockets/unicorn.sock fail_timeout=0;
      }

      server {
        listen 80;
        server_name example.com;

        location /app {
          proxy_pass  http://rails_app_server;
          proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
          proxy_redirect off;
          proxy_buffering off;
          proxy_set_header        Host            $host;
          proxy_set_header        X-Real-IP       $remote_addr;
          proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
          error_log /path/to/railsapp/log/nginx/error.log;
          access_log /path/to/railsapp/log/nginx/access.log;
          break;
        }
      }


### Make Assets work with subdirectory

* configure prefix for assets in config/application.rb

      config.assets.prefix  = '/app/assets/'

* make assets prefix work on Nginx
    
      location ~ ^/app/assets/  {
        root /path/to/railsapp/public;
        gzip_static on;
        expires max;
        add_header Cache-Control public;
        # access_log /dev/null;
      }

