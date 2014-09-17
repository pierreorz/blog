---
layout: post
title: "动态改变LOV的值集"
date: 2014-09-17 18:01
comments: true
categories: ADF
tags:
- ADF
- LOV
---

#### <i class="icon-file"></i>   需求：

LOV的值集需要根据传入的用户角色类型(A,B,C)动态改变，A时查询所有，B时过滤字段1，C时过滤字段2.

 **基础VO的SQL如下:**
{% codeblock lang:sql %}
 SELECT DISTINCT U.USER_ID, U.USER_NAME, F.FULL_NAME AS DESCRIPTION
  FROM FND_USER U, PO_HEADERS_ALL P, PO_VENDORS V, PER_PEOPLE_F F
 WHERE U.EMPLOYEE_ID = P.AGENT_ID
   AND P.VENDOR_ID = V.VENDOR_ID
   AND F.PERSON_ID(+) = U.EMPLOYEE_ID
   AND P.APPROVED_FLAG = 'Y'
   AND ((P.VENDOR_ID = :VARVENDORID AND :VARUSERTYPE = 'B') OR
       (:VARUSERTYPE = 'A') OR
       (:VARUSERTYPE = 'C' AND U.USER_ID = :VARUSERID))
{% endcodeblock %}
创建VARUSERTYPE VARUSERID VARVENDORID 三个绑定变量。

#### <i class="icon-folder-open"></i> 创建LOV VO

 **LVO的SQL如下:**
{% codeblock lang:sql %}
 SELECT NULL         AS USER_ID,
       NULL         AS USER_NAME,
       NULL         AS DESCRIPTION,
       :VARTYPE     AS USERTYPE,  
       :VARVENDORID AS VENDORID,
       :VARUSERID   AS CURRENTUSERID
  FROM DUAL
{% endcodeblock %}
技巧在这里，LVO同样创建三个绑定变量，并把绑定变量作为VO的attribute暴露出来，然后通过view Accessors传递给基础VO

#### <i class="icon-pencil"></i> 设置View Accessors

- 创建LOV
![](../../../wp-content/uploads/QQ20140916174007.png)

- 设置View Accessors
![](../../../wp-content/uploads/QQ20140916174112.png)

这样就可以实现动态切换LOV的基础数据源了。
