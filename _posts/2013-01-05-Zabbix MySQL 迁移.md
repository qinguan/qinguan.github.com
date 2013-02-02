---
layout: post
title: Zabbix MySQL 迁移
---

Zabbix MySQL 迁移
========================
05 Jan 2013 - Beijing 
 
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
 
 
