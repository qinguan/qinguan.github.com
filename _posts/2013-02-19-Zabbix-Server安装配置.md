---
layout: post
title: Zabbix Server 2.0.4安装配置
---

Zabbix Server 2.0.4安装配置
========================
19 Feb 2013 - Beijing


Enviroment: CentOS-6.3-i386-minimal.iso + virtual Box

# 配置虚机网络:
************************************************	
	## bridge network or hostonly network
	# vi /etc/sysconfig/network-scripts/ifcfg-eth0
	# /etc/rc.d/init.d/network restart
	# chkconfig network on
************************************************

# 安装相关软件包：	
************************************************	
	# yum install wget make gcc mysql mysql-devel libcurl-devel \
	net-snmp-devel openldap-devel libssh2-devel OpenIPMI-devel  \
	java-1.7.0-openjdk java-1.7.0-openjdk-devel mysql-server httpd
	# yum install php php-mysql php-bcmath php-mbstring php-gd php-xml
************************************************

# 添加zabbix账号:
************************************************	
	# groupadd zabbix
	# useradd -g zabbix zabbix
************************************************

# 编译安装zabbix：
************************************************	
	# wget http://sourceforge.net/projects/zabbix/files/ZABBIX%20Latest%20Stable/2.0.4/zabbix-2.0.4.tar.gz/download
	# tar -zxvf zabbix-2.0.4.tar.gz
	#./configure --enable-server --enable-agent --with-mysql \
	--enable-ipv6 --with-net-snmp --with-libcurl --with-ldap \
	--with-ssh2 --with-openipmi --enable-java
	# make && make install
************************************************

# 启动mysql并进行配置:
************************************************	
	# service mysqld start
	# mysql
		create database zabbix character set utf8;
		grant all privileges on zabbix.* to 'zabbix'@'%.%.%.%' identified by 'zabbix';
		flush privileges;
	mysql -u zabbix -p zabbix < /root/zabbix-2.0.4/database/mysql/schema.sql
	mysql -u zabbix -p zabbix < /root/zabbix-2.0.4/database/mysql/images.sql
	mysql -u zabbix -p zabbix < /root/zabbix-2.0.4/database/mysql/data.sql
************************************************

# 修改zabbix server配置文件:
************************************************	
	# vi /usr/local/etc/zabbix_server.conf
	DBUser=root --> zabbix
	DBpassword=zabbix
************************************************

# 修改PHP配置文件:
************************************************	
	# vi /etc/php.ini
************************************************
	# line 440: change to Zabbix recomended
	max_execution_time = 600
	# line 449: change to Zabbix recomended
	max_input_time = 600
	# line 457: change to Zabbix recomended
	memory_limit = 256M
	# line 729: change to Zabbix recomended
	post_max_size = 32M
	# line 878: change to Zabbix recomended
	upload_max_filesize = 16M
	# line 946: uncomment and add your timezone
	date.timezone = Asia/Shanghai
************************************************

# 拷贝前端代码:
************************************************	
	# mkdir -p /var/www/zabbix
	# cp /root/zabbix-2.0.4/frontends/php/* /var/www/zabbix/ -R
************************************************	

# 修改Apache配置:
************************************************		
	# vi /etc/httpd/conf.d/zabbix.conf
		Alias /zabbix /var/www/zabbix
		<Directory "/var/www/zabbix">
		Options FollowSymLinks
		AllowOverride None
		Order allow,deny
		Allow from all # change to the range you allow to access
		</Directory>
************************************************

# 修改前端数据库配置：
************************************************	
	mv /var/www/zabbix/conf/zabbix.conf.php.example /var/www/zabbix/conf/zabbix.conf.php
	chmod 777 /var/www/zabbix/conf/zabbix.conf.php
	vi /var/www/zabbix/conf/zabbix.conf.php
		$DB["TYPE"]                             = 'MYSQL';
		$DB["SERVER"]                   = 'localhost';
		$DB["PORT"]                             = '0';
		$DB["DATABASE"]                 = 'zabbix';
		$DB["USER"]                             = 'zabbix';
		$DB["PASSWORD"]                 = 'zabbix';
************************************************

# 启动脚本:
************************************************	
	# cat restart_zabbix.sh
		#!/bin/bash
		service iptables stop
		setenforce 0
		ps -ef | grep zabbix_server | grep -v grep | awk '{print $2}' | xargs kill 
		ps -ef | grep zabbix_agentd | grep -v grep | awk '{print $2}' | xargs kill
		service mysqld restart
		/usr/local/sbin/zabbix_server
		/usr/local/sbin/zabbix_agentd
		/etc/rc.d/init.d/httpd restart
************************************************	

#zabbix server is not running问题:
当未关闭selinux时，zabbix frontend会不断提示zabbix server is not running。可以通过以下命令查看selinux状态。
	[root@localhost ~]# getenforce status
	Permissive
	[root@localhost ~]# 
若显示为enable，则需要setenforce 0.具体可参考：<https://support.zabbix.com/browse/ZBX-5423>

相关链接：
+ <https://www.zabbix.com/documentation/2.0/manual/installation/install>
