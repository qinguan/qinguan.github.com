---
layout: post
title: Zabbix Proxy 配置
---

Zabbix Proxy配置
========================
12 April 2013 - Beijing

当Zabbix监控的机器越来越多的时候，Zabbix Server以及后端数据库压力会很大，尤其是数据库，并发读写操作异常频繁。
目前有两种方式可以缓解，一是采用Node方式，一是采用Proxy方式。


Proxy部署脚本见： [zabbix_proxy_install](https://github.com/qinguan/zabbix_configure/blob/master/zabbix_proxy_install.sh)
	
相关参考链接：
	
+ <https://www.zabbix.com/documentation/doku.php?id=2.0/manual/distributed_monitoring/proxies>

+ <https://www.zabbix.com/documentation/2.0/manual/appendix/config/zabbix_proxy>
+ <http://blogs.reliablepenguin.com/2012/10/13/zabbix-proxy-install>
+ <http://anyways.lofter.com/post/8287e_4a4d18>
+ <https://www.zabbix.com/documentation/2.0/manual/appendix/install/db_scripts>
	