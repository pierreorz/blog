---
layout: post
title: "配置OAF开发环境"
date: 2013-04-17 15:01
comments: true
categories: OAF
tags: 
- OAF
- DBC
- 配置环境
---
####查看EBS系统OA版本信息####
   
方法一：使用OPERATIONS用户登陆EBS系统，选择“Diagnostics”后，在页下脚选择“About this Page”后选择“Technology Components”标签可查看相应版本信息。

方法二：访问URI：    
{% codeblock lang:java %}
http://<HOST>:<PORT>/OA_HTML/OAInfo.jsp    
{% endcodeblock %}

####查看OAF版本下载相应JDEV开发工具####
metalink document id: 787209.1

例如：p4141787_11i_GENERIC.zip

解压缩后把“/jdevhome/jdev”路径加到系统变量中，变量名：      JDEV_USER_HOME=F:\p4141787_11i_GENERIC\jdevhome\jdev

####配置DBC文件####
确认当前EBS系统使用的dbc文件.

连接EBS数据库，执行以下SQL查询：
{% codeblock lang:sql %}
select host_name||'_'||instance_name from v$instance;
{% endcodeblock %}

telnet到EBS系统，查找dbc文件的的位置并拷贝下来放在jdevhome\jdev\dbc_files\secure目录下:    
{% codeblock lang:java %}
ls –a|$FND_SECURE/*.dbc
{% endcodeblock %}

检验DBC文件是否EBS系统正在使用的DBC可用以下命令：    
{% codeblock lang:java %}
java oracle.apps.fnd.security.AdminAppServer apps/apps STATUS DBC=/u01/dev/devappl/fnd/11.5.0/secure/SIT.dbc
{% endcodeblock %}

确认系统是否使用这个dbc文件可以在路径
$APPL_TOP/admin/[SID]_[host].xml
查找_dbc_file_name，跟上面说的dbc文件一致即可

####配置JDEV工程属性####

以上准备工作都完成之后，配置JDEV的Preferences中编码为UTF-8    
![](../../../wp-content/uploads/QQ截图20130417144216.jpg)    

配置project setting中编译器编码    
![](../../../wp-content/uploads/QQ截图20130417144309.jpg)

然后配置DBC文件路径，测试EBS账户名及密码，所属职责等信息如下图所示：    
![](../../../wp-content/uploads/QQ截图20130417144424.jpg)



