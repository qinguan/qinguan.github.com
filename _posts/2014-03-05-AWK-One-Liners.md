---
layout: post
title: AWK One Liners
description: ""
category: AWK
tags: [AWK]
---

AWK One Liners
======

patten { action }

###### 统计行数

    awk 'END { print NR }' infile   
    === wc -l

###### 打印第5行

    awk 'NR == 5' infile 

###### 打印每行最后一个字段值

    awk '{ print $NF }' infile

###### 打印最后一行最后一个字段

    awk '{ field = $NF } END{ print field} ' infile 

###### 打印字段数大于4的每一行

    awk 'NF > 4' infile

###### 打印最后字段值大于4的每一行

    awk '$NF > 4' infile

###### 打印所有行所有字段总数

    awk ' { nf = nf + NF }END{ print nf } ' infile  
    === wc -w

###### 打印包含指定内容(awk)的行总数  
    awk '/awk/ { nf = nf + 1 } END{ print nf }' infile

    awk -f awk_sh infile  
    awk_sh:  
    /awk/ { nf = nf + 1 }  
    END{ print nf }  


###### 打印首字段值最大的一行  
    awk '{ max=0; if($1 > max) max = $1; maxline = $0} END {print max, maxline}' infile   
    or  
    awk '$1 > max { max = $1; maxline = $0} END {print max, maxline}' infile   

###### 打印至少有一个字段的行(过滤空行)  
    awk 'NF > 0' infile  

###### 打印长度超过10字符的行  
    awk 'length($0) > 10' infile  

###### 打印行号和内容  
    awk '{ print NR, $0}' infile  
    === cat -n infile  
    === nl infile   

###### 打印每行前两个字段，并置换位置  
    awk ' {print $2, $1}' infile  

###### 置换每行前两个字段，打印全行  
    awk ' { temp = $1; $1 = $2; $2 = temp; print}' infile   

###### 将第一个字段作为行号输出每行内容  
    awk ' { $1 = NR; print }' infile  

###### 擦除第二个字段输出每一行  
    awk ' { $2=""; print}' infile  

###### 逆序输出每一行的字段  
    awk '{for(i=NF;i>0;i--) printf("%s ",$i);printf("\n")}' infile  

###### 每行字段求和  
    awk '{ sum=0; for(i=1;i<=NF;i++) sum += $i; }END {print sum}' infile  
    eg: seq 1 3 | nl | awk '{ sum=0; for(i=1;i<=NF;i++) sum += $i; }END {print sum}'   
    output: 6  

###### 所有行所有字段之和  
    awk '{ for(i=1;i <=NF; i++) sum += $i;} END {print sum}' infile  

###### 打印所有行的绝对值  
    awk ' { for(i=1; i<=NF; i++) if($i < 0) $i = - $i; print}' infile  
    eg：seq -3 3 | awk ' { for(i=1; i<=NF; i++) if($i < 0) $i = - $i; print}'   


+ <http://www.amazon.cn/mn/detailApp/?asin=020107981X>  