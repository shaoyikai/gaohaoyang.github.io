---
layout: post
title:  "nginx负载均衡很简单！"
date:   2017-05-22
categories: nginx
tags: ngixn 负载均衡 
excerpt: 如何搭建nginx负载均衡？
---


### 环境

开启三个虚拟主机，IP地址分别为：
```shell

172.23.158.82
172.23.158.129
172.23.158.130

```


### 配置负载均衡服务器，以82服务器为入口服务器

```shell
upstream myweb{

    server 172.23.158.82 weight=2;
    server 172.23.158.129 weight=5;
    server 172.23.158.130 weight=3;
}
server {
    listen 80;
    root /home/yikai/www/test;
    server_name localhost
    location / {
        proxy_pass http://myweb;
    }
}

```



