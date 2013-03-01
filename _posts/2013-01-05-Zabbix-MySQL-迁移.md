---
layout: post
title: Zabbix MySQL 迁移及表分区
---

Zabbix MySQL 迁移及表分区
========================
05 Jan 2013 - Beijing 
 
1. 迁移
====

导出本地数据并关闭数据库：
	mysqldump -u root -p password > /tmp/zabbix_db_dump
	service mysql stop
在新机器上创建数据库并导入：
	Create database zabbix;
	mysql -u root -p password < /tmp/zabbix_db_dump
	mysql -u root -p password
创建zabbix用户:
	create user ‘zabbix’@'IP Address’ identified by ‘Password’;
	use zabbix;
	grant all privileges on zabbix.* to ‘zabbix’@'IP Address’;
	flush privileges;
	exit;
注: IP Address 可选择使用'%.%.%.%'模式。
	mysql -u zabbix -p -h MySQLServerIPAddress
	
配置zabbix服务：
First, stop Zabbix Agent and Server.
	sudo /etc/init.d/zabbix-agent stop
	sudo /etc/init.d/zabbix-server stop
Edit the following file: /etc/zabbix/zabbix_server.conf.Search for DBHost. 
Update the line to:
	DBHost=IP Address
	DBPassword=Password
重启zabbix服务
	sudo /etc/init.d/zabbix-server start
	sudo /etc/init.d/zabbix-agent start
配置zabbix前端：
Edit the following file: /zabbix/conf/zabbix.conf.php. 
Search for $DB["SERVER"] and change the ip address to IP Address.
	$DB['SERVER'] = 'IP Address';
	$DB['PASSWORD'] = 'password';


2. 表分区
=====

注； 分区主要针对history_*表

a. Zabbix自身有housekeeper机制，会定时删除相应的历史数据，但是该操作效率低下。
b. 采用: disable housekeeper, partition table, create and drop table partitions.

history_*共5张表:
	| history_log           |
	| history_str           |
	| history_sync          |
	| history_text          |
	| history_uint          |

更改history_*表Primary Key:
	ALTER TABLE `history_log` DROP PRIMARY KEY, ADD PRIMARY KEY (`itemid`,`id`,`clock`);
	ALTER TABLE `history_log` DROP KEY `history_log_2`;
	ALTER TABLE `history_text` DROP PRIMARY KEY, ADD PRIMARY KEY (`itemid`,`id`,`clock`);
	ALTER TABLE `history_text` DROP KEY `history_text_2`;

分区:	
	alter table `history` partition by range(clock)(
	partition p20130228 values less than (unix_timestamp("2013-03-01 00:00:00")), 
	partition p20130301 values less than (unix_timestamp("2013-03-02 00:00:00")), 
	partition p20130302 values less than (unix_timestamp("2013-03-03 00:00:00")), 
	partition p20130303 values less than (unix_timestamp("2013-03-04 00:00:00")), 
	partition p20130304 values less than (unix_timestamp("2013-03-05 00:00:00")) 
	);

创建mysql procedures：
	source zabbix-mysql-autopartitioning.sql;
[zabbix-mysql-autopartitioning.sql](https://github.com/qinguan/zabbix_configure/blob/master/zabbix-mysql-autopartitioning.sql)
	
创建crontab,定时删除/创建分区：
	55 23 * * * root mysql -h DBHOST -P PORT -u USER -p DB -e "CALL create_zabbix_partitions();" >> /tmp/zabbix_mysql_partition.log
		
当历史数据很大的时候，比如说上百G，建议先truncate table,清空数据;

相关参考:


+ <zabbixzone.com/zabbix/partitioning-tables/>

+ <http://qinguan.github.com/2013/02/20/MySQL-%E8%A1%A8%E5%88%86%E5%8C%BA.html>
+ <http://www.cnblogs.com/gqdw/archive/2013/01/01/2841221.html>