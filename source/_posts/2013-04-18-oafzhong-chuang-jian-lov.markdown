---
layout: post
title: "OAF中创建LOV"
date: 2013-04-18 12:54
comments: true
categories: OAF
tags: 
- OAF
- LOV
---
环境：Oracle9i Jdeveloper+R11i

####首先创建AM####

创建一个LovAM，默认配置即可。

####创建VO####

创建LovVO，不需要基于EO，输入查询SQL如下：
{% codeblock lang:sql %}
select distinct INVENTORY_ITEM_ID,concatenated_segments 
from MTL_SYSTEM_ITEMS_KFV where organization_id=85
{% endcodeblock %}

将创建好的VO添加到AM的data module中

####创建RN####

右键工程，创建一个RN，Scope设置为public.    
![](../../../wp-content/uploads/QQ截图20130418113938.jpg)    

选中创建好的LovRN.xml，在structure Window窗口中选中LovRN右建New Region Using Wizard.找到之前创建的LovVO.    

![](../../../wp-content/uploads/QQ截图20130418114930.jpg)

修改字段属性如下：     
Search Allowed : true     
Selective Search Criteria : true    
![](../../../wp-content/uploads/QQ截图20130418115100.jpg)

####创建PG页面引用LOV####

创建一个PG页面LovPG.xml，AM Definition设置为之前创建的LovAM路径;    
Windows title & tile 随意设置；    
在LovPG页面中创建一个Region改名为mainRN，Region Style为messageComponentLayout;    
在mainRN中创建一个MessageLovInput控件item1，    修改item1的属性External LOV值为此前步骤中创建好的LovRN路径；
prompt改为：物料LOV    
lovMap1中，设置Lov Region Item 为InventoryItemId;     Return Item为item1;Criteria Item为item1;    
运行该lovPG.xml，即可看到效果如下图：

![](../../../wp-content/uploads/QQ截图20130418124811.jpg)    
![](../../../wp-content/uploads/QQ截图20130418125343.jpg)
