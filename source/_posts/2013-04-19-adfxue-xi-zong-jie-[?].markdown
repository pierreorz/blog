---
layout: post
title: "ADF学习总结(一)"
date: 2013-04-19 14:15
comments: true
categories: ADF
tags: 
- ADF 
- ViewObject 
- ViewCriteria
---
####改变VO查询条件####

- 使用WhereClause改变查询条件
- 使用Criteria改变查询条件
- 使用Bind Variables改变查询条件

####使用WhereClause改变查询条件####
- **setWhereClause**    

通过VO对象的setWhereClause方法改变查询条件
{% codeblock lang:java %}
DCIteratorBinding dc = ADFUtils.findIterator("DepartmentsView1Iterator");    
ViewObject vo = dc.getViewObject();       
vo.setWhereClause(" 1=1 ");    
vo.executeQuery();     
{% endcodeblock %}

- **addWhereClause**

addWhereClause是在原有whereClause基础上添加新的查询条件；    
跟setWhereClause替换原有whereClaue不同；

####使用ViewCriteria改变查询条件####

- **使用定义好的ViewCriteria**    

事先在创建VO的时候，定义好几种Criteria：DepartmentsViewCriteria    
![](../../../wp-content/uploads/QQ截图20130419134617.jpg)    

{% codeblock lang:java %}
DCIteratorBinding dc = ADFUtils.findIterator("DepartmentsView1Iterator");        
ViewObject vo = dc.getViewObject();           
ViewCriteriaManager vcm=vo.getViewCriteriaManager();    
vo.applyViewCriteria(vcm.getViewCriteria("DepartmentsViewCriteria"));     
vo.executeQuery();     
{% endcodeblock %}

- **动态创建ViewCriteria**    

如果事先没有在VO中定义好ViewCriteria，也可以在MB代码中动态创建    

{% codeblock lang:java %}
DCIteratorBinding dc = ADFUtils.findIterator("DepartmentsView1Iterator");    
ViewObject vo = dc.getViewObject();    
ViewCriteria vc = vo.createViewCriteria();    
ViewCriteriaRow vcr = vc.createViewCriteriaRow();    
vcr.setConjunction(ViewCriteriaRow.VC_CONJ_AND);    
vcr.setAttribute("DepartmentId", "20");    
vc.add(vcr);    
vo.applyViewCriteria(vc);    
vo.executeQuery();        
{% endcodeblock %}

####使用Bind Variables改变查询条件####

- **使用Bind Variables结合ViewCriteria**

参照以上定义ViewCriteria时，使用Bind Variables。    

- **使用Bind Variables结合SQL**

在VO的SQL中使用Bind Variables；    

{% codeblock lang:sql %}
SELECT Departments.DEPARTMENT_ID, 
       Departments.DEPARTMENT_NAME, 
       Departments.MANAGER_ID, 
       Departments.LOCATION_ID
FROM DEPARTMENTS Departments
WHERE Departments.DEPARTMENT_ID = :varDeptId
{% endcodeblock %}

创建Bind Variables：varDeptId    
![](../../../wp-content/uploads/QQ截图20130419140752.jpg)    

在查询VO时，通过如下代码控制即可实现：    

{% codeblock lang:java %}
DCIteratorBinding dc = ADFUtils.findIterator("DepartmentsView1Iterator");    
ViewObject vo = dc.getViewObject();    
ViewCriteria vc = vo.createViewCriteria();    
vo.setNamedWhereClauseParam("varDeptId", 20);    
vo.executeQuery();          
{% endcodeblock %}

以上几种方法均可实现改变VO查询条件进行动态查询，各有灵活度，目前没有比较三种方式带来的性能影响，可以根据个人习惯采用。




