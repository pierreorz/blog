---
layout: post
title: "由意外断电导致oracle11g启动不起来的问题解决"
date: 2013-11-09 19:24
comments: true
categories: Oracle
tags: 
- Oracle 11g
- lsnrctl
- rpm2cpio
---

- 平台: linux + oracle 11g    

- 故障: 意外断电之后，监听启动不了

####解决方法####

执行`lsnrctrl start`命令时报错误代码如下： 

`TNS-12537`    
`TNS-12560`    
`TNS-00507`    
`Linux Error:29`    

- 检查/etc/hosts文件，没有发现异常。    

- 因为未改动任何配置文件，所以基本可以确定是断电造成文件损坏而引起。于是使用`relink all`看看能否解决该问题。
{% codeblock lang:java %}
 # cd $ORACLE_HOME/bin    
 # relink all
{% endcodeblock %}
执行`relink all`命令之后，再执行`lsnrctrl start`时，发现不报之前的错误了，出现了新的错误：
{% codeblock lang:java %}
 symbol lookup error: $ORACLE_HOME/lib/libclntsh.so.11.1    
: undefined symbol: nnftboot
{% endcodeblock %}


- 看来是这个libclntsh.so.11.1出了问题，于是尝试去下载该文件替换掉。   

- 从RPM search网站上下载到oracle-instance-client的RPM文件到本地。

- 使用rpm2cpio命令抽取出里面的libclntsh.so.11.1

{% codeblock lang:java %}     
#rpm2cpio oracle-instance-client-xxx.rpm | cpio -div    
{% endcodeblock %}

- 将得到的libclntsh.so.11.1文件替换掉$ORACLE_HOME/lib下的重名文件即可。    

