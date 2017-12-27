---
layout: post
title:  "服务器运维备注"
date:   2017-12-27
categories: linux
tags: linux
excerpt: 服务器运维常用命令
---

nginx

```bash
/etc/init.d/nginx start
/etc/init.d/nginx stop
/etc/init.d/nginx restart
```

nginx 典型配置

```
server {
    listen       80;
    server_name  example.com *.example.com;
    root         /usr/share/nginx/example;

    location / {
        index index.php index.html index.htm;
        rewrite ^/(.*)$ /index.php?_url=/$1;
    }

    error_page 404 /404.html;
        location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    }

    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME   $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

    location ~ ^/favicon\.ico$ {
    
    }
    
    location ~* ^/(css|img|js|flv|swf|download)/(.+)$ {    
 
    } 
}

server {
    listen 443;
    server_name example.com *.example.com;
    ssl on;
    root example;
    index index.php index.html index.htm;

    ssl_certificate /etc/nginx/conf.d/cert/some_name.pem;
    ssl_certificate_key /etc/nginx/conf.d/cert/some_name.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    
    location / {
        root example;
        index index.php index.html index.htm;
    }
}
```

php-fpm

```bash
/etc/init.d/php-fpm start
/etc/init.d/php-fpm stop
/etc/init.d/php-fpm restart
```

