---
layout: post
title: "如何获取web.xml中的context-param参数"
date: 2013-09-04 19:30
comments: true
categories: ADF
tags:
- context-param
- web.xml
---
**通过EL表达试获取**

以web.xml中的<context-param>参数：javax.faces.FACELETS_VIEW_MAPPINGS为例,
可通过以下EL获取。    
{% codeblock lang:java %}    
${initParam['javax.faces.FACELETS_VIEW_MAPPINGS']}    
{% endcodeblock %}    

如果参数名称很简单，比如：testParameter，EL表达式也可以写成以下方式：     
{% codeblock lang:java %}    
${initParam.testParameter}    
{% endcodeblock %} 

**通过JAVA代码获取**

如果需要在MB方法中获取该参数值，可以参照以下代码：
{% codeblock lang:java %}    
    public String action() {
        FacesContext fctx=FacesContext.getCurrentInstance();
        ExternalContext ec=fctx.getExternalContext();
        ServletContext servletContext=(ServletContext)ec.getContext();
        String value=servletContext.getInitParameter("javax.faces.FACELETS_VIEW_MAPPINGS");
        System.out.println(value);
        return null;
    }  
{% endcodeblock %} 
