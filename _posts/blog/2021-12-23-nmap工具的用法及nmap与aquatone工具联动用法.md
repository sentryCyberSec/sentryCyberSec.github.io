---
layout: post
title: "nmap工具的用法及nmap与aquatone工具联动用法"
date: 2021-12-23
wrench: 2021-12-23
author: D3ch4ng
toc: true
categories:
- blog
tags:
- NMAP
permalink: /blog/2021/nmap工具的用法及nmap与aquatone工具联动用法/
---

# nmap工具的用法

nmap -T4 -A -v  完整全面扫描

![image-20211208173459200.png](https://s2.loli.net/2021/12/15/mM5L1zydhC8ki3t.png)

nmap用于主机发现，端口扫描，版本扫描的参数：

nmap -Pn IP:扫描主机开放端口及MAC地址

![image-20211208170657902.png](https://s2.loli.net/2021/12/15/h9M56UQetWGmzy3.png)

nmap IP :用nmap扫描特定的IP地址

![image-20211208170808240.png](https://s2.loli.net/2021/12/15/1TWd5QlpGIXivC3.png)

nmap -vv IP：用-vv对结果进行详细输出

![image-20211208170941087.png](https://s2.loli.net/2021/12/15/WSQtd9Uag7XY4mk.png)

nmap -p1-端口 IP：自行设置端口范围进行扫描

![image-20211208171058830.png](https://s2.loli.net/2021/12/15/JuKpHl7A1tyoa4W.png)

nmap -p端口,端口,端口 IP：指定端口号进行扫描

![image-20211208171135236.png](https://s2.loli.net/2021/12/15/mN7dIf8Geq6XtHM.png)

nmap -sP IP：对目标进行ping扫描，来判断主机是否存活

![image-20211208171209966.png](https://s2.loli.net/2021/12/15/pCk4OUeBulbSwzQ.png)

nmap --traceroute IP：进行路由跟踪

![image-20211208171253895.png](https://s2.loli.net/2021/12/15/8u3NeylQxCIDfnW.png)

nmap -sP 192.168.1.0/24 :扫描一个网段的主机在线状况

![image-20211208171407391.png](https://s2.loli.net/2021/12/15/yFiSrfPc6VQAENl.png)

nmap -O IP：对指定IP进行操作系统扫描

![image-20211208171450089.png](https://s2.loli.net/2021/12/15/Pobf8Bi2JQUcWGw.png)

nmap -A IP：全面系统检测、启用脚本检测、扫描

![image-20211208171937568.png](https://s2.loli.net/2021/12/15/RjwD1MpfLIEgvmP.png)

nmap -sV IP：指定nmap进行端口服务版本扫描

![image-20211208172342620.png](https://s2.loli.net/2021/12/15/nMuEbzrJ9UG3sgK.png)

nmap -sn 192.168.1.0/24 :进行主机发现，不进行端口扫描

![image-20211208170025458.png](https://s2.loli.net/2021/12/15/sAXPyQFj8rJqEkx.png)

nmap -PO IP:使用ip协议包探测对方主机是否开机

![image-20211208172630553.png](https://s2.loli.net/2021/12/15/SFmJoyhKI1wOebd.png)

nmap -sT IP:TCP connect()扫描，这种方式会在目标主机的日志中记录大批连接请求和错误信息

![image-20211208173018779.png](https://s2.loli.net/2021/12/15/SYnaC1tcX6KWkFE.png)

![image-20211208173247229.png](https://s2.loli.net/2021/12/15/5C2WpakcobSgxEy.png)

# nmap 联动 aquatone工具

discover参数  发现：aquatone评估的第一阶段是发现阶段，确定目标的主域名服务器，使用主域名服务器进行发现。

aquatone-discover -d tantanapp.com

![image-20211210151730161.png](https://s2.loli.net/2021/12/15/gkxpnG1N7bHKmEe.png)

aquatone-discover模块 发现的结果保存为hosts.txt ，提取的子域名地址；

![image-20211214104203715.png](https://s2.loli.net/2021/12/15/TB2Dkx3JXHvdIR1.png)

首先把nmap -iL single-tantanDoanins.xml使用该文件作为输入，保存的nmap结果参数有 -oX :文件以XML文件格式保存 ，-oN：标准格式保存，-OA：保存到所有格式，-oG：Grep格式保存

命令如下：nmap -iL hosts-sigle.txt -oX tantanDoamins.xml -p- -A -v -sV 

![image-20211214172804782.png](https://s2.loli.net/2021/12/15/oxS7tO1BygialZh.png)

cat tantanDoamins.xml | aquatone -nmap -chrome-path /root/chrome-linux/chrome 执行命令后 生成三个文件后缀为html，json,txt。

![image-20211215135904819.png](https://s2.loli.net/2021/12/15/kLVXc46OTF1yrq5.png)

打开文件后缀为html的文件，扫描结果对应IP的页面就展现出来了。

![image-20211215153203849.png](https://s2.loli.net/2021/12/15/ryKCMWhtigsQf7c.png)
