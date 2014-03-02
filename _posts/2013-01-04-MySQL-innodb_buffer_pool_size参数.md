---
layout: post
title: MySQL innodb_buffer_pool_size参数
description: ""
category: MySQL
tags: [MySQL]
---

MySQL innodb_buffer_pool_size参数
========================
04 Jan 2013 - Beijing

该参数定义了 InnoDB 存储引擎的表数据和索引数据的最大内存缓冲区大小,适当的增加这个参数的大小，可以有效的减少 InnoDB类型的表的磁盘 I/O 。在一个以 InnoDB为主的专用数据库服务器上，可以考虑把该参数设置为物理内存大小的60%-80%。

查看mysql的缓存大小命令：
	SHOW GLOBAL VARIABLES LIKE "%buffer_pool%";
可以通过编辑/etc/my.cnf,添加下面的语句来修改缓存大小:
	innodb_buffer_pool_size = 2G

参考资料：

+ <http://blog.chinaunix.net/uid-17282739-id-3201157.html>	