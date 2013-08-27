---
layout: post
title: "利用contextual event刷新Region"
date: 2013-06-01 21:20
comments: true
categories: ADF
tags: 
- contextual event
- region
- ADF
- refresh
---
-----
平台： Jdev 11.1.1.7.0    
需求：两个region，其中一个region对数据进行修改后，需要刷新另外一个region显示   
---
----
####创建bound taskflow: btf_form####
![../../../wp-content/uploads/QQ截图20130601204146.jpg](../../../wp-content/uploads/QQ截图20130601204146.jpg)    

taskflow只包括一个页面碎片empForm.jsff，拖入employeeVO生成af:Form。    
![../../../wp-content/uploads/QQ截图20130601210114.jpg](../../../wp-content/uploads/QQ截图20130601210114.jpg)    
 
###创建bound taskflow: btf_table####
同上，只有一个页面碎片empTable.jsff，拖入employeeVO生成af:table。     
![../../../wp-content/uploads/QQ截图20130601210258.jpg](../../../wp-content/uploads/QQ截图20130601210258.jpg)        

以上两个taskflow皆使用No Controller Transaction.

####添加contextual event####
需要在empTable.jsff页面中修改数据，单击commit按钮时，让empForm.jsff页面中的数据即时更新。因此，给commitbutton添加contextual event: saveEvent。    
![../../../wp-content/uploads/QQ截图20130601210725.jpg](../../../wp-content/uploads/QQ截图20130601210725.jpg)    

####创建mainPG页面引入以上taskflow生成region####

创建一个两栏jspx页面mainPG.jspx， 分别拖入以上两taskflow生成region。    
给contextual event 绑定consumer。    

切换到mainPG.jspx页面的pagedef页面，从structure窗口中执行以下操作；    
![../../../wp-content/uploads/QQ截图20130601211338.jpg](../../../wp-content/uploads/QQ截图20130601211338.jpg)    

![../../../wp-content/uploads/QQ截图20130601211438.jpg](../../../wp-content/uploads/QQ截图20130601211438.jpg)    

producer即为contextual event的事件源，由btf_table中的commitbutton产生，consumer即为btf_form中的execute消耗，即btf_table中每commit一次，btf_form即执行一次execute操作刷新页面。

以上只是简单了解下contextual event的工作原理，contextual event还可以带参在region之间传值等。
