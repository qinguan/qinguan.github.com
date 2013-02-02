---
layout: post
title: OpenLDAP Replication
---

OpenLDAP Replication
========================
05 Jan 2013 - Beijing

mirror方式，该模式不支持对两台LDAP服务器同时写操作。

编辑/etc/openldap/slapd.conf,添加如下:
	serverID        1
	moduleload syncprov.la
	 
	overlay syncprov
	syncprov-checkpoint 100 10
	syncprov-sessionlog 100
	syncrepl        rid=001
					provider=ldaps://ldapserver.xuguojun.com:636
					bindmethod=simple
					binddn="cn=Manager,dc=xuguojun,dc=com"
					credentials=123456
					searchbase="dc=xuguojun,dc=com"
					schemachecking=on
					type=refreshAndPersist
					retry="60 +"
					starttls=yes
					tls_cacert=/etc/openldap/cacerts/cacert.pem
	mirrormode on
重启ldap server：
	service slapd restart

注：另外一台Openldap Server配置雷同，但需要修改serverID，以及provider.

两台LDAP Server使用了同样的CA签名，即在同一台机器上签发了两个证书(针对不同机器的hostname修改了证书的CN).

相关参考资料：

+ <http://www.openldap.org/doc/admin24/replication.html>
