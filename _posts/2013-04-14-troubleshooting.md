---
layout: post
title: Troubleshooting
---

Troubleshooting
========================
20 April 2013 - Beijing

*************************
Problem: Linux时间不同步

Solution:
ubuntu:
	sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime  (注:上海时间)
	sudo /usr/sbin/ntpdate cn.pool.ntp.org

*************************
Problem: Xshell终端乱码

Solution:
Session Proterties->Terminal->Encodeing: change "Default Language" to "Unicode(UTF-8)"

(临时修改: export LANG=en_US.UTF-8)

*************************
Problem: no version information available (required by /usr/lib/libcurl.so.4)

	$ curl localhost:8090/hello.html
	curl: /usr/local/lib/libldap_r-2.4.so.2: no version information available (required by /usr/lib/libcurl.so.4)
	curl: /usr/local/lib/liblber-2.4.so.2: no version information available (required by /usr/lib/libcurl.so.4)
	hello world
	$
Solution:
	sudo ln -fs /usr/lib/liblber-2.4.so.2 /usr/local/lib/
	sudo ln -fs /usr/lib/libldap_r-2.4.so.2 /usr/local/lib/

备注:
	-f, --force			强行删除任何已存在的目标文件
	-s, --symbolic		创建符号链接而非硬链接
	
同类问题:	
	git: /usr/local/lib/liblber-2.4.so.2: no version information available (required by /usr/lib/libcurl-gnutls.so.4)
	
*************************
Problem: git push提示username & password

Solution:
	use git not https,for example:
	vim ~/.git/config
	change:
	[remote "origin"]
			fetch = +refs/heads/*:refs/remotes/origin/*
			url = https://github.com/qinguan/SMScripts.git

	to:
	[remote "origin"]
			fetch = +refs/heads/*:refs/remotes/origin/*
			url = git@github.com:qinguan/SMScripts.git

	done
	