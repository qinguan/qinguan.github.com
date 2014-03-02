---
layout: post
title: 根据IP记录Linux用户SSH登陆终端操作历史命令
description: ""
category: Linux
tags: [SSH, Linux]
---

根据IP记录Linux用户SSH登陆终端操作历史命令!
=====================================================
22 Dec 2012 - Beijing

在/etc/profile配置文件的末尾加入以下脚本代码，或者单独生成一个文件，置于/etc/profile.d/目录下，实现SSH远程登陆用户IP地址和终端操作的日志记录。

	PS1="`whoami`@`hostname`:"'[$PWD]'
	history
	USER_IP=`who -u am i 2>/dev/null| awk '{print $NF}'|sed -e 's/[()]//g'`
	if [ "$USER_IP" = "" ]
	then
		USER_IP=`hostname`
	fi
	if [ ! -d /tmp/history ]
	then
		mkdir /tmp/history
		chmod 777 /tmp/history
	fi
	if [ ! -d /tmp/history/${LOGNAME} ]
	then
		mkdir /tmp/history/${LOGNAME}
		chmod 300 /tmp/history/${LOGNAME}
	fi
	export HISTSIZE=4096
	DT=`date +"%Y%m%d_%H%M%S"`
	export HISTFILE="/tmp/history/${LOGNAME}/${USER_IP} history.$DT"
	chmod 600 /tmp/history/${LOGNAME}/*history* 2>/dev/null


相关参考资料:

+ <http://www.ha97.com/4733.html>
+ <http://blog.csdn.net/sunboy_2050/article/details/6322892>
+ <http://www.linuxquestions.org/questions/linux-general-1/etc-profile-v-s-etc-bashrc-273992/>