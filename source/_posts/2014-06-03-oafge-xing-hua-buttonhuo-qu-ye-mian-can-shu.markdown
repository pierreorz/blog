---
layout: post
title: "OAF个性化Button获取页面参数"
date: 2014-06-03 11:19
comments: true
categories: OAF
tags: 
- OAF
---

- 平台: R12    
- 需求: 需要个性化一个button的Destination URI属性，并添附上一个参数。

实现方法：

1. 本来重写CO便很容易实现该需求，但是涉及到修改代码以及各个环境更新就很麻烦了，所以如果个性化button的URL是最方便了。

2. 个性化又涉及到如何在URL里传递参数的问题。  

右键当前页面，查看源代码如下：      

{% codeblock lang:html %}
...

<form id="DefaultFormName" name="DefaultFormName" style="margin:0px" method="POST" action="/OA_HTML/OA.jsp?page=/oracle/apps/pa/deliverable/webui/CrUpDeliverablePG&paCallingPage=DLVLIST&paCallingMode=VIEW&paProjectId=106691&paDeliverableId=113287&paDlvrItemId=106962&&addBreadCrumb=RP&_ti=1696581738&PersonalizationParam=PersonalizationParamAdmin&retainAM=Y&oapc=28">

...

{% endcodeblock %}

需要将当前页面中form标签的action属性中的paDlvrItemId参数的值获取过来。拼接成形如:http://dengdezhao.cn?erpid={:paDlvrItemId}形式的URL赋给button的destinationURI属性。

3. 使用{@itemName}这种方式只能在table控件中才行，在此处不适用。因此考虑使用JS查找当前页面获取到参数然后拼接形成URL。

{% codeblock lang:html %}

http://192.168.15.141/login/LoginSSO.jsp?flowCode=AM02&erpid='+unescape(document.DefaultFormName.action.match(new RegExp("(^|&)" + 'paDlvrItemId' + "=([^&]*)(&|$)", "i"))[2])+'&workcode=' + document.getElementById('AdditionalInfo').rows[4].cells[1].innerText + '

{% endcodeblock %}


4. 将以上URL个性化赋值给button即可满足需求。

