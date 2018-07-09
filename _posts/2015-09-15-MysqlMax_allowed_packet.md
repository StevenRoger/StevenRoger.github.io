---
title: MYSQL数据库max_allowed_packet设置
description: Windows下使用MYSQL数据库出现Packet for query is too large的问题，提示说需要修改max_allowed_packet的大小。
categories:
- mysql
tags:
- mysql
---

mysql根据配置文件会限制server接受的数据包大小。
有时候大的插入和更新会被max_allowed_packet 参数限制掉，导致失败。

查看目前配置

> show VARIABLES like '%max_allowed_packet%';

显示的结果为：
```
+--------------------+---------+
| Variable_name      | Value   |
+--------------------+---------+
| max_allowed_packet | 1048576 |
+--------------------+---------+
```
以上说明目前的配置是：1M

## 修改方法

### 方法1

可以编辑my.cnf来修改（windows下my.ini）,在[mysqld]段或者mysql的server配置段进行修改。
> max_allowed_packet = 20M

如果找不到my.cnf可以通过
> mysql --help | grep my.cnf
去寻找my.cnf文件。

### 方法2

进入mysql server

在mysql 命令行中运行

> set global max_allowed_packet = 2 * 1024 * 1024* 10
然后关闭掉这此`mysql server`链接，再进入。

> show VARIABLES like '%max_allowed_packet%';
查看下max_allowed_packet是否编辑成功

## 总结

在很多台机器上用方法一都没问题，但2011年11月14日遇到一台机器死活都不成功，

使用命令行方式：
> set global max_allowed_packet = 16M;

也不行，但使用

> set global max_allowed_packet = 2 * 1024 * 1024 * 10;

成功了，很是郁闷疑问

问题终于找出来了，不是方法的问题，是设置完成后要把命令行退出重新登录查看，看来系统变量的值在登录后会缓存。但在这台机器上使用配置INI文件的方式就是不行，具体原因还没找到。