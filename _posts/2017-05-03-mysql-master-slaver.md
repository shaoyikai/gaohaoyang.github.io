---
layout: post
title:  "MYSQL主从配置与读写分离"
date:   2017-05-03
categories: mysql
tags: 主从配置 负载均衡 读写分离
excerpt: mysql读写分离，大流量高并发解决方案
---


### 原理图片

![Alt text](/image/2017/mysql-master-salve-proxy.jpg "mysql master and slaver")


mysql 主从复制和读写分离是两个概念，主从复制是读写分离的基础，只有部署好了主从复制，才能在此基础上进行读写分离的操作。

master服务器负责write，slaver服务器负责read。

a.基于程序代码内部实现
在代码中根据select、insert进行路由分类。
优点是性能好，不需要增加额外的设备作为硬件开支；
缺点是需要开发人员实现，增加了开发的难度，对于已经开发完毕的代码，还需要重新修改测试，如果是复杂的系统，这又是一场灾难。


b.基于中间代理实现，不如mysql官方的 `MySQL-Proxy`

说了这么多，可能很多人会有疑问，具体怎么实施呢？下面我们来详细说明下具体的流程。


### 配置MYSQL主从复制

三个ubuntu虚拟机，ip如下：
  * 172.23.158.82   用作master
  * 172.23.158.102  用作slave1
  * 172.23.158.101  用作slave2

好文参考

http://www.serverlab.ca/tutorials/linux/database-servers/how-to-create-a-mysql-master-slave-cluster-on-ubuntu-14/

http://www.cnblogs.com/crazylqy/p/5542558.html


### 读写分离

http://blog.jobbole.com/94606/

http://www.cnblogs.com/lin3615/p/5684891.html

nginx负载均衡
mysql主从复制与读写分离

修改mysql-proxy的脚本
进入到/usr/share/mysql-proxy/目录下，修改rw-splitting.lua
