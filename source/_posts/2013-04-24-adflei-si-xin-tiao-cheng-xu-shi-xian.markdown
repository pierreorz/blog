---
layout: post
title: "ADF类似心跳程序实现"
date: 2013-04-24 16:19
comments: true
categories: ADF
tags: 
- adf
- heartBeat
- <af:poll/>
---
使用ADF框架开发WEB应用时，其中令人苦恼的一个需求就是：在两个页面同时展示一个数据源时，如果其中一个页面对数据源作了变更操作，另外一个页面没办法自动获得变更后的数据，需要手工刷新页面才能得到变更后的新数据源。   

其实ADF自带的`<af:poll/>`组件即可实现此需求。

例子：

- 创建一个可更新的af:table 基于HR的Department表

页面如下：   
![](../../../wp-content/uploads/QQ截图20130424160613.jpg)    
可以更改表数据并提交，用多浏览器窗口打开该页面来模拟多用户操作。

将`<af:poll/>`控件拖入到页面中，控件属性设置如下：    

![](../../../wp-content/uploads/QQ截图20130424161027.jpg)    
设置间隔为1分钟，并绑定PollListener

  
- 给该页面绑定ManagedBean，并添加PollListener，方法内容如下：    

{% codeblock lang:java %}
    public void heartBeat(PollEvent pollEvent) {
        System.out.println("start poll...");        
        DCIteratorBinding it = ADFUtils.findIterator("Departments1Iterator");
        ViewObject vo = it.getViewObject();
        vo.executeQuery();
        AdfFacesContext.getCurrentInstance().addPartialTarget(JSFUtils.findComponentInRoot("t1"));
       }
{% endcodeblock %}    

每隔1分钟自动查询一次VO，并将更新后的内容返回到页面；    

- 这样打开两个页面之后，在其中一个页面更改内容并提交后，也会即时更新另外一个页面（另外一个客户端）；

PS：此例子频繁地查询VO，在实际项目中会对服务器造成很大的压力，只为展示`<af:poll>`控件作用，不建议使用此方案。

