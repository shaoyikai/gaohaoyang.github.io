---
layout: post
title:  "nginx负载均衡"
date:   2017-05-22
categories: nginx
tags: nginx 负载均衡
excerpt: 如何配置nginx负载均衡？
---


### 环境

开启三个虚拟主机，IP地址分别为

`172.23.158.82` \\
`172.23.158.129` \\
`172.23.158.130`

### 配置负载均衡服务器，以82服务器为入口服务器

```shell
upstream myweb{
		#ip_hash;
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

### 问题一

用户第一次访问被随机到了服务器82，第二次访问又随机到了服务器129，如何保证用户的session不丢失？

网上有几种解决方案，这里只介绍最简单的一种：如上示例代码，打开`ip_hash`的注释即可实现用户ip绑定到固定的server，从而避免session丢失引起的麻烦。

### 问题二

用户访问时，先经过代理服务器根据权重随机分发到相应的服务器上。这些服务器上的业务逻辑是一样的，可是如何保证数据的一致性呢？连接同一个数据库吗？

推测：现实中，瓶颈一般在数据库上，需要用到nginx做负载均衡的时候，数据库更需要做主从复制与读写分离了，也就是数据库集群。既然有了集群，nginx负载均衡的这些应用代码统一连接到同一个数据库集群上应该就可以了。

> 如有不对的地方，欢迎拍砖，欢迎交流。
