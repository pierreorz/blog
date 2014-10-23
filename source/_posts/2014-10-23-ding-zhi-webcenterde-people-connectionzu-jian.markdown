---
layout: post
title: "定制webcenter的people connection组件"
date: 2014-10-23 22:17
comments: true
categories: webcenter
tags:
- webcenter
- people connection
---

#### <i class="icon-file"></i>   需求：

webcenter的people connection类似于微博的关注粉丝功能，现在需要利用程序让某一用户自动关注一批人员。比如某一用户所在项目`(PROJECT1)`下有10个成员，则该用户进入portal的时候默认就展示其项目下的10个成员在某一分组`(PROJECT1)`下。

#### <i class="icon-folder-open"></i> 分析：

根据项目PROJECT1可以查询出10个成员名；
利用API将10个成员添加到Connections中；
利用API创建Connection list，也即是分组名`(PROJECT1)`；
将10个成员的Connection 添加到分组PROJECT1中。


#### <i class="icon-pencil"></i> 技术实现：

- 根据项目PROJECT1可以查询出10个成员名；
在AMimpl中创建查询成员名方法以List返回结果。
{% codeblock lang:sql %}
	public List<String> getAllMembersByProjectId(String proId){
        List<String> result=new ArrayList<String>();
        result.add("CG02");
        result.add("CG03");
        result.add("CG04");
        result.add("CG05");
        result.add("pierre");
        result.add("weblogic");
        //TODO 根据项目ID proId查询出项目所有成员以List返回
        return result;
    }
{% endcodeblock %}


- 利用API将10个成员添加到Connections中；
oracle.webcenter.peopleconnections.connections.internal.model.ConnectionsManagerImpl.createConnection 方法可以给两个guid创建Connection

{% codeblock lang:sql %}
ConnectionsServiceFactory fac=ConnectionsServiceFactory.getInstance();
ConnectionsManagerImpl cm=(ConnectionsManagerImpl)fac.getConnectionsManager();
 cm.createConnection(ownerUserId, connectUserId);
{% endcodeblock %}

- 利用API创建Connection list，也即是分组名`(PROJECT1)`；
ConnectionListsManager.createConnectionList 可以创建分组
{% codeblock lang:sql %}
 ConnectionsServiceFactory fac=ConnectionsServiceFactory.getInstance();
 fac.getConnectionListsManager().createConnectionList("PROJECT1");
{% endcodeblock %}

- 将10个成员的Connection 添加到分组PROJECT1中。
利用API ConnectionListsManager.addMembersToConnectionList方法可以将创建好的Connection添加到分组中去。样例代码如下：
{% codeblock lang:sql %}
ConnectionsServiceFactory fac=ConnectionsServiceFactory.getInstance();
fac.getConnectionListsManager().addMembersToConnectionList(List, "PROJECT1");
{% endcodeblock %}

创建Webcenter Portal Server Extension应用，创建TaskFlow调用以上方法；发布扩展应用到spaces，重启spaces，定义resource catalog应用到portal上即可。
