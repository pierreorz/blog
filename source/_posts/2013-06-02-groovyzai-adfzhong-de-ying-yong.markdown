---
layout: post
title: "Groovy在ADF中的应用"
date: 2013-06-02 11:14
comments: true
categories: ADF
tags: 
- ADF
- Groovy
- max
- min
- avg
- count
- sum
---
使用Groovy脚本，在开发ADF应用中可以极大地提高开发效率，实现某些看似很复杂的功能。

比如在empVO中获取sequence值:    
{% codeblock lang:java %}
db = adf.object.getDBTransaction()    
sq = new oracle.jbo.server.SequenceImpl("EMPLOYEES_SEQ",db)    
sq.getSequenceNumber()    
{% endcodeblock %}    

**在empVO中获取Salary字段最大值max：**    
{% codeblock lang:java %}
adf.object.getRowSet().max("Salary")
{% endcodeblock %}

**在empVO中获取Salary字段最小值min：**    
{% codeblock lang:java %}
adf.object.getRowSet().min("Salary")
{% endcodeblock %}

**在empVO中获取Salary字段合计sum：**    
{% codeblock lang:java %}
adf.object.getRowSet().sum("Salary")
{% endcodeblock %}

**在empVO中获取Salary字段均值avg：**    
{% codeblock lang:java %}
adf.object.getRowSet().avg("Salary")
{% endcodeblock %}

**在empVO中获取Salary字段数量count：**    
{% codeblock lang:java %}
adf.object.getRowSet().count("Salary")
{% endcodeblock %}

**import JAVA类库**    
导入SimpleDateFormat类，对当前日期格式化成String对象输出    
{% codeblock lang:java %}
import java.text.SimpleDateFormat
df = new SimpleDateFormat("yyyy-MM-dd")
df.format(adf.currentDate)
{% endcodeblock %}

**获取当前行中Attribute值**    
{% codeblock lang:java %}
source.Salary 
source.getAttribute("Salary")
{% endcodeblock %}

**调用EntityImpl中的方法**   
在EntityImpl中定义的方法均可以通过**adf.object**.yourMethod()方式调用    
其它如：    
**adf.object.entityState**    
**adf.object.postState**    
**adf.userSession** //获得Session   
**adf.context** //获得AdfContext对象     

**调用AM中的自定义方法getHelloWorld()**    
{% codeblock lang:java %}
structureDef.getApplicationModule().getHelloWorld()
{% endcodeblock %}

Groovy脚本的用处还有很多，以上只是总结了部分常用的调用方法。
