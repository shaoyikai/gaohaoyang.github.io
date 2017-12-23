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

### 配置MYSQL主从复制

三个ubuntu虚拟机，ip如下：
  * 172.23.158.82   用作master
  * 172.23.158.102  用作slave1
  * 172.23.158.101  用作slave2

好文参考

------------------------------------------------------------
### 配置`master`服务，修改 /etc/mysql/my.cnf

bind-address            = 172.23.158.82

```shell

 server-id              = 82
 log_bin                = /var/log/mysql/mysql-bin.log
 expire_logs_days        = 10
 max_binlog_size         = 100M
 binlog_do_db           = first_db
```
重启mysql

```shell
sudo service mysql restart
```
### 创建Replication用户

1. 使用命令行登陆mysql
2. 创建一个账户
```shell
mysql > CREATE USER 'repl'@'172.23.158.82' IDENTIFIED BY '123456';
```
3. 授予其replication权限
```shell
mysql > GRANT REPLICATION SLAVE ON *.* TO 'repl'@'172.23.158.82';
```
4. 刷新权限
```shell
mysql > GRANT REPLICATION SLAVE ON *.* TO 'repl'@'172.23.158.82';
```

### 查看Master信息
```shell
mysql> show master status;
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000010 |      107 | first_db     |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)

```

记录 File 和 Position 值


### Configure the Slaves

bind-address            = 172.23.158.101

```shell

 server-id              = 101
 log_bin                = /var/log/mysql/mysql-bin.log
 expire_logs_days        = 10
 max_binlog_size         = 100M
 binlog_do_db           = first_db
```

重启mysql
```shell
sudo service mysql restart
```
### Enable Replication

1. Log into the MySQL console.
mysql -u root -p
2. Configure connection to master server.
```shell
CHANGE MASTER TO
MASTER_HOST='172.23.158.82',
MASTER_USER='repl',
MASTER_PASSWORD='123456',
MASTER_LOG_FILE='mysql-bin.000010',
MASTER_LOG_POS=107;
```

3. Start slave to enable replication.

```shell
START SLAVE;
```

这样mysql主从复制就配置好了。
http://www.serverlab.ca/tutorials/linux/database-servers/how-to-create-a-mysql-master-slave-cluster-on-ubuntu-14/

--------------------------------------------------------------------------


http://www.cnblogs.com/crazylqy/p/5542558.html


### 读写分离

http://blog.jobbole.com/94606/

http://www.cnblogs.com/lin3615/p/5684891.html

nginx负载均衡
mysql主从复制与读写分离

修改mysql-proxy的脚本
进入到/usr/share/mysql-proxy/目录下，修改rw-splitting.lua

启动mysql-proxy

sudo mysql-proxy --defaults-file=/etc/mysql-proxy.cnf
netstat -tupln | grep 4040
mysql -uproxy -h 172.23.158.101 -P 4040 -pproxy123



[mysql全文索引好文](http://www.cnblogs.com/feichexia/archive/2012/06/09/2543049.html)