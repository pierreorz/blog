---
layout: post
title: "Attribute set for xxxAttribute in view object xxxVO failed问题解决"
date: 2013-06-19 15:50
comments: true
categories: OAF
tags: 
- OAF
- Jdeveloper
- checkbox
- 单选
---

----
平台：Jdeveloper 9i 
----

异常描述：     

在表增加单选checkbox时，在VO中创建transient属性selector,最终在页面中无法勾选checkbox，发现值没法保存到VO中，页面抛出该异常：  
{% codeblock lang:java %}	
null - Attribute set for Selector in view object employeeVO1 failed
{% endcodeblock %}  

解决方案：    

1. 检查VO中的transient属性selector是否设置为updatable
2. 如果是在开发模式下，清空所有classes文件，重新编译；如果是在正式环境，删除该路径下的VO对象，可使用以下命令。    
{% codeblock lang:java %}
exec jdr_utils.deletedocument('/oracle/apps/dengdezhao/test/server/employeeVO');    
{% endcodeblock %}


