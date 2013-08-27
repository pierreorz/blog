---
layout: post
title: "Centos下安装mysql5.6后忘记ROOT密码，重置密码"
date: 2013-06-25 16:01
comments: true
categories: mysql
tags:
- mysql
- centos
---

**安装**

去[oracle官网](https://edelivery.oracle.com/EPD/Search/handle_go)下载mysql5.6 for linux安装介质V38444-01.zip。    
解压缩之后    
{% codeblock lang:java %}
MySQL-client-advanced-5.6.12-1.el6.i686.rpm
MySQL-devel-advanced-5.6.12-1.el6.i686.rpm
MySQL-embedded-advanced-5.6.12-1.el6.i686.rpm
MySQL-server-advanced-5.6.12-1.el6.i686.rpm
MySQL-shared-advanced-5.6.12-1.el6.i686.rpm
MySQL-shared-compat-advanced-5.6.12-1.el6.i686.rpm
MySQL-test-advanced-5.6.12-1.el6.i686.rpm
{% endcodeblock %}

开始安装    
{% codeblock lang:java %}
rpm -ivh MySQL-*
{% endcodeblock %}

Mysql5.6的安全机制加强了，默认安装时会随机给ROOT用户生成一个密码保存在安装用户目录下/root/.mysql_secret中。由于没认真看英文文档，我直接安装完就把这个文件里的随机密码给改掉了，就无法得知ROOT初始密码，也无法登录进数据库了，囧。

**重置Mysql初始密码**

用ROOT用户登录，STOP数据库   
{% codeblock lang:java %}
/etc/init.d/mysql stop
{% endcodeblock %}

跳过授权表启动Mysql服务器     
{% codeblock lang:java %}
mysqld_safe --skip-grant-tables&    
{% endcodeblock %}

输入CTRL+C中断进程后应该可以看到mysqld已经在运行了    

{% codeblock lang:java %}
mysql --user=root mysql

update user set Password=PASSWORD('new-password');
flush privileges;
exit; 
{% endcodeblock %}

操作完以上变更密码的命令后，可以重启mysqld了。    

{% codeblock lang:java %}
killall mysqld_safe&    
{% endcodeblock %}

按CTRL+C退出，接着正常启动mysql

{% codeblock lang:java %}
/etc/init.d/mysql start
{% endcodeblock %}

用刚刚修改的新密码登录mysql即可    

{% codeblock lang:java %}
mysql -uroot -p    
{% endcodeblock %}





