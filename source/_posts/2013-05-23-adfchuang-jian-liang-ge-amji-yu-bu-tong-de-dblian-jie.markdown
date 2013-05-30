---
layout: post
title: "ADF创建两个AM基于不同的DB连接"
date: 2013-05-23 13:48
comments: true
categories: ADF
tags: 
- ADF
- AM
- JNDI
- JDBC
---
----------
平台：Jdev11.1.2.3.0

需求描述：    
有两个表，基于不同的Schema（或者基于不同的数据库)，需要在project中创建两个基于不同DB连接的AM/EO/EO等供页面使用；

---------
以HR/HR 跟 SCOTT/TIGER 为例：    

- ****创建DB连接****

![../../../wp-content/uploads/QQ截图20130523131132.jpg](../../../wp-content/uploads/QQ截图20130523131132.jpg)    
![../../../wp-content/uploads/QQ截图20130523131209.jpg](../../../wp-content/uploads/QQ截图20130523131209.jpg)  

- ****创建AM/EO/VO****    
 
1. 右键MODEL工程属性，先将DB连接初始为HR
![../../../wp-content/uploads/QQ截图20130523131810.jpg](../../../wp-content/uploads/QQ截图20130523131810.jpg)    
2. 创建HrAM及EmployeesEO，EmployeesVO；
3. 重复1~2步骤，将MODEL工程属性的DB连接切换为SCOTT并创建ScottAM及DeptEO,DeptVO;

目前HrAM的DB连接基于HR Schema，并且data model是基于HR下的Employees表；ScottAM的DB连接基于SCOTT Schema，data model基于SCOTT下的DEPT表；

- ****修改bc4j.xcfg文件****    
由于设置DB连接时会自动修改AM的配置文件将Custom JDBCDataSource替换成一致的，所以需要手工修改bc4j.xcfg文件，给两个AM配置不同的JDBCdatasource；    

右建任一AM，选configurations，修改以下内容如图：    

![../../../wp-content/uploads/QQ截图20130523133007.jpg](../../../wp-content/uploads/QQ截图20130523133007.jpg)    

- ****修改工程的weblogic deployment属性****    
右键Application，选Applicatoin properties,设置如下图：

![../../../wp-content/uploads/QQ截图20130523133613.jpg](../../../wp-content/uploads/QQ截图20130523133613.jpg)

不需要在发布时自动配置JDBC连接描述文件，因为下一步会在weblogic中配置JDBC数据源，如果该项勾选上可能会在发布时出现问题不能同时连接两个Schema；    

- ****配置JDBC数据源****    
启动Jdev自带的Weblogic服务器；进入[http://127.0.0.1:7101/console](http://127.0.0.1:7101/console "Weblogic控制台")    
配置如下两个数据源hrDS/scottDS（JDNI名与bc4j.xcfg中配置的保持一致)    
![../../../wp-content/uploads/QQ截图20130523134225.jpg](../../../wp-content/uploads/QQ截图20130523134225.jpg)

- ****测试效果****    
创建一个测试页面，将HrAM/ScottAM中的Datacontrol拖到页面上生成两个af:table查看运行之后能否取出数据；

![../../../wp-content/uploads/QQ截图20130523134714.jpg](../../../wp-content/uploads/QQ截图20130523134714.jpg)
