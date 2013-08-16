---
title: Make Nginx As Front End Proxy For Apache
layout: post
category: Tech
tags: [Nginx, Apache]
---


As requirements, after deploy a Rails app on Nginx, we need to setup a Wordpress
as official blog, and make sure they share the same domain and work at different
subdirectories, such as `domain.com/app` for rails, `domain.com/blog` for wordpress.

On Wordpress official blog, [Apache](http://httpd.apache.org/) is the recommend web server, so we
intend to use Apache and Nginx together, Nginx handle static files and 
proxy the dynamic requests to Apache.


#### Why use Nginx in front of Apache

As far as I known, Nginx is a non-blocking event based architecture while Apache is a blocking process based architecture. Nginx is more lightful and has better performance on handling static files than Apache, always used as a reverse proxy first, HTTP server second. It can be easily used as a load balancer by [Upstream Module](http://wiki.nginx.org/HttpUpstreamModule), powers high-performance and small memory footprinti. However, it is not better than Apache for handling dynamic requests, and if you want to make php work on Nginx, you may need intall php extension, such as [php-fpm](http://php-fpm.org/). Apache has built-in php module. So a common scenario is to use Nginx and Apache together and use Nginx as reverse proxy.

#### How to configure Nginx in front of Apache

Requirements:

- Apache is listening on 8080 port
- Nginx is running and listening on 80 port

A good habit to add a nginx configuration is put it under /etc/nginx/sites-available and make a link to actviate it at /etc/nginx/sites-enable, here I use example.com as example:

      upstream apache_php {
        server example.com:8080;
      }

      server {
        listen 80;
        server_name example.com;

        location /blog {
          proxy_pass  http://apache_php;
          proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
          proxy_redirect off;
          proxy_buffering off;
          proxy_set_header        Host            $host;
          proxy_set_header        X-Real-IP       $remote_addr;
          proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
          break;
        }

        location / {
          rewrite ^(.*)$ /blog last;
        }
      }

      server {
        listen 80;
        server_name www.example.com;
        rewrite ^/(.*) http://example.com/$1 permanent;
      }

As proxied backend, a VirtualHost configuration for Apache is required:

    <VirtualHost *:8080>
      ServerName example.com
      ServerAlias www.example.com
      DocumentRoot /var/www
      ErrorLog /var/www/blog/logs/error.log
      CustomLog /var/www/blog/logs/access.log combined
      <Directory />
        Options FollowSymLinks
        AllowOverride None
      </Directory>
      <Directory /var/www>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride None
        Order allow,deny
        allow from all
      </Directory>
    </VirtualHost>

After the configuration, we need restart Nginx and Apache to make the changes work.

    sudo /etc/init.d/nginx restart
    sudo /etc/init.d/apache2 restart

Now we can directly visit wordpress blog at http://example.com.


About how to organize Rails app and Wordpress with different subdirectory under the same domain, I will cover it in [my next blog](http://marshluca.com/2013/08/10/make-nginx-as-front-end-proxy-for-apache/).

