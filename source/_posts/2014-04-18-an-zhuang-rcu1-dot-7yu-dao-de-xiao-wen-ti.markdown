---
layout: post
title: "安装RCU1.7遇到的小问题"
date: 2014-04-18 20:24
comments: true
categories: SOA
tags: 
- RCU
---

- 环境：此前安装过soa1.6，最近重新安装soa1.7时遇到的问题
- 安装Rcu1.7时，发现无法运行./rcu,错误提示如下：


{% codeblock lang:java %}
rcuHome/jdk/jre/bin/java: cannot execute binary file.
{% endcodeblock %}

查看操作系统发现是32bit,而rcu只有64bit的。

{% codeblock lang:java %}
$getconf LONG_BIT    
32    
{% endcodeblock %}

在32bit系统上安装64bit软件向下兼容应该是可以安装的，因此怀疑是jdk的问题。

修改rcu文件最后几行的jre_home 为中间件文件夹中的JDK：/home/pierre/Oracle/Middleware/jdk160_24/bin/java 后，重新运行即可解决该问题。


- 成功运行rcu之后，进入配置oracle数据库信息页，一直报 invalid service 错误, 原来是service name 要加上机器名后缀 orcl.localdomain即正常。

-  因为之前安装过rcu1.6，所以需要将此前的mds删除掉，不然在创建soa domain的时候会出现错误。






