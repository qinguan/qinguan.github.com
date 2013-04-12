---
layout: post
title: Zabbix Proxy 配置
---

Zabbix Proxy配置
========================
12 April 2013 - Beijing

当Zabbix监控的机器越来越多的时候，Zabbix Server以及后端数据库压力会很大，尤其是数据库，并发读写操作异常频繁。
采用Proxy方式部署Zabbix服务，可以在一定程度上缓解后端数据库的压力。
Proxy可以采用[源码方式](https://www.zabbix.com/documentation/1.8/manual/installation#zabbix_proxy1)安装，也可以直接用[RPM包安装](https://github.com/qinguan/zabbix_configure/blob/master/zabbix_proxy_install.sh)。

Proxy部署完之后,需要再前端管理界面进行配置：
1. 登陆Web界面，Administration -> DM.
2. 点击Create Proxy 按钮
3. 在"Proxy name" 填入Proxy Server的hostname
4. 保存
5. zabbix agent配置文件中的Server指向Zbbix Server的IP/hostname
6. host配置中选择"Monitored by proxy"

Zabbix Proxy部署脚本见： [zabbix_proxy_install](https://github.com/qinguan/zabbix_configure/blob/master/zabbix_proxy_install.sh)
	
相关参考链接：
	
+ <https://www.zabbix.com/documentation/doku.php?id=2.0/manual/distributed_monitoring/proxies>

+ <https://www.zabbix.com/documentation/2.0/manual/appendix/config/zabbix_proxy>
+ <http://blogs.reliablepenguin.com/2012/10/13/zabbix-proxy-install>
+ <http://anyways.lofter.com/post/8287e_4a4d18>
+ <https://www.zabbix.com/documentation/2.0/manual/appendix/install/db_scripts>
	