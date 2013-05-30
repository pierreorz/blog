---
layout: post
title: "Jdeveloper运行速度优化"
date: 2013-04-04 17:41
comments: true
categories: jdeveloper 
tags:
- jdeveloper
- AddVMOption
---
####*本文适合木有高配机却不得不在JDEV下开发的屌丝程序员*####

####先来了解下Jdev的两个配置文件####

{% codeblock lang:java %}
E:\Oracle\Middleware11.1.2.3.0\jdeveloper\ide\bin\ide.conf    
E:\Oracle\Middleware11.1.2.3.0\jdeveloper\jdev\bin\jdev.conf
{% endcodeblock %}

几个关键参数：
{% codeblock lang:java %}
AddVMOption  -Xmx        //堆大小的最大值，在机器物理内存允许的基础上该值越大越好
AddVMOption  -Xms        //堆大小的初始值（默认给个128差不多）
AddVMOption  -XX:MaxPermSize //PermGen大小，太小会报OutOfMemoryError错误  
{% endcodeblock %}

这几个参数设置得不合适的话JDEV会无法启动并报以下错误：
{% codeblock lang:java %}
Unable to create an instance of the Java Virtual Machine Located at path:
E:\Oracle\Middleware11.1.2.3.0\jdk160_24\jre\bin\client\jvm.dll
{% endcodeblock %}

好了~，我们现在需要做的就是将这几个参数的值设置得适合它的大小，以便JDEV运行得到最佳性能

因为我的机器是32位系统WIN2003 6G内存，系统吃掉2G左右，可用内存不到4G
而32位的JDK最大好像也只能申请1.5G左右,所以在我这机器中Jdev能分配到1G内存就差不多了

先随意设置以上三个参数值，将JDEV能够运行起来，然后使用JDK自带的工具来监控下内存情况
E:\Oracle\Middleware11.1.2.3.0\jdk160_24\bin\jvisualvm.exe
![](../../../wp-content/uploads/20130404.jpg)

根据图中**堆**及**PermGen**中显示使用大小调整三个参数值。
发现在不断操作JDev时PermGen始终维持在128左右不到256的样子，
所以设置    
-XX:MaxPermSize值为`AddVMOption  -XX:MaxPermSize=256M`    
-Xms值为`AddVMOption  -Xms128M`    
通过不断测试最终调整-Xmx值为`AddVMOption  -Xmx896M`

若在运行中出现OutOfMemoryError错误再根据信息慢慢调整。当然最好还是升级机器配置换成64位的系统及JDK就没有内存限制啦！~


