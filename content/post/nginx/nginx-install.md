---
title: "Nginx安装和基本使用"
date: 2019-09-27T13:56:20+08:00
lastmod: 2019-09-27T13:56:20+08:00
description: ""
draft: false
tags: ["nginx", "install"]
categories: ["nginx"]
---

Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。Nginx是由伊戈尔·赛索耶夫为俄罗斯访问量第二的Rambler.ru站点（俄文：Рамблер）开发的，第一个公开版本0.1.0发布于2004年10月4日。
其将源代码以类BSD许可证的形式发布，因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。2011年6月1日，nginx 1.0.4发布。
Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在BSD-like 协议下发行。其特点是占有内存少，并发能力强

## Install
* platform: Centos7

* version: 7.2

* 安装

  ```bash
  wget http://nginx.org/download/nginx-1.16.1.tar.gz
  tar -zxvf nginx-1.16.1.tar.gz
  cd nginx-1.16.1
  sudo ./configure && make && make install
  whereis nginx # 查看nginx安装地址  /usr/local/nginx
  ```

## BasicUse
* 启动
  ```bash
  /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
  ```
  
* 重启
  ```bash
  cd /usr/local/nginx/sbin
  ./nginx -s reload
  ```

## Example Conf

```nginx
# /usr/local/nginx/conf/nginx.conf

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    # 包含aps的nginx配置
    include /usr/local/nginx/conf/aps/*.conf;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
}
```

```nginx
# /usr/local/nginx/conf/aps/aps.conf
server {
        listen 10050;
        server_name localhost;

        # 访问后端api
        location /api/ {
             proxy_pass http://127.0.0.1:5500/;
             proxy_set_header Host            $host;
             proxy_set_header X-Real-IP       $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        # 访问静态文件
        location /static/ {
            alias /usr/local/nginx/html/aps/dist/;   # 静态文件访问硬盘
        }

        # 访问主页
        location / {
            root /usr/local/nginx/html/aps/dist/;
            index index.html index.htm;
        }
}
```





