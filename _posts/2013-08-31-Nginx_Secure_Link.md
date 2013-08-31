---
layout: post
title: Nginx Secure Link
---

Nginx Secure Link
========================
31 Aug 2013 - Beijing	
	
nginx有一个比较好用的模块，ngx_http_secure_link_module，能够通过对请求参数校验，实现非法请求过滤。

要在nginx里支持该功能，配置nginx时需要添加参数:
	./configure --with-http_secure_link_module ---(其它配置)

该模块主要的两个指令:
	secure_link $arg_st;
	secure_link_md5 $uri;
st是待校验的请求参数，uri是请求地址，secure_link_md5将对改地址做md5。

	example:
	server {
        listen 8088;

        #add_header Content-Disposition "attachment;";

        location /test {
                secure_link $arg_st;
                secure_link_md5 $uri;

                 ## If the hash is incorrect then $secure_link is a null string.
                if ($secure_link = "") {
                        echo $secure_link;
                        return 403;
                }

                ## The current local time is greater than the specified expiration time.
                if ($secure_link = "0") {
                        return 403;
                }
                echo "uri:" $uri
                echo $secure_link;
        }
    }

	#curl http://10.15.180.12:8088/test?st=RTkzBki4D5TvO_kR9td6yQ
	uri: /test echo 1

在上面这个例子中，uri是/test,st是RTkzBki4D5TvO_kR9td6yQ。st参数生成方法如下:
	# echo -n '/test' | openssl md5 -binary | openssl base64 | tr +/ -_ | tr -d =
	RTkzBki4D5TvO_kR9td6yQ
st如何生成可以由用户和nginx协商定义。secure_link_md5支持变量拼接，如secure_link_md5 "$secure_link_expires$uri$remote_addr secret";	

nginx接到请求后首先会做个校验，对uri进行md5，然后跟st匹配。如不符，直接返回403。

相关链接:

+ <http://nginx.org/en/docs/http/ngx_http_secure_link_module.html>
