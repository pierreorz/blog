---
layout: post
title: "OAF单选删除行功能"
date: 2013-06-24 11:15
comments: true
categories: OAF
tags:
- OAF 
- Jdeveloper 9i
- ViewObject 
- Delete
---
----
平台：Jdeveloper 9i    

---
**一般实现步骤：**
 
1. 在VO中添加新transient属性selector，updatable设置为always。    
2. 在PG中给table添加singleSelection方法，并绑定view Instance为VO的instance，View Attribute为刚刚新建的transient属性seletor。        
3. 这样当用户勾选checkbox时，会给transient属性变量赋值为Y，因此，在删除逻辑中循环判断VO中的transient属性，当其为Y时执行remove该行即可实现选择删除功能。    
**相关代码如下：**

**CO中processFormRequest里添加代码如下：**

{% codeblock lang:java %}
	//当单击deleteBtn按钮时执行AM中的deleteAutoSequenceVO方法    
    if (pageContext.getParameter("deleteBtn")!=null)
    {
      am.invokeMethod("deleteAutoSequenceVO");

    }
{% endcodeblock %}

**AM中代码如下：**

{% codeblock lang:java %}
    public void deleteAutoSequenceVO() {
        OAViewObject vo = this.getChAutoSequenceVO1();
        RowSetIterator iter = vo.createRowSetIterator("delete");
        if (vo != null) {
            int num = vo.getRowCount();
            for (int i = 0; i < num; i++) {
                Row temp = iter.next();
                if ("Y".equals(temp.getAttribute("selector"))) {
                    vo.setCurrentRow(temp);
                    vo.removeCurrentRow();
                    break;
                }
            }
        }
        iter.closeRowSetIterator();
        throw new OAException("删除成功,请点击[保存]按钮生效到数据库.",    
                              OAException.INFORMATION);
    }

{% endcodeblock %}


但是在实际开发中，出现一个很诡异的现象，其它页面单选删除功能都正常，某一页面单选之后却无法赋值Y给VO中的transient变量，推测可能是VO用的是复合主键或者其它框架BUG导致，无奈给单选再添加手工赋值功能。    

选择单选控件，将其Client Action中的Action Type设置为firePartialAction Submit设置为false。准备在用户单选时调用自定义方法，给变量赋值。

**CO中processFormRequest里添加如下方法**
{% codeblock lang:java %}
        if ("update".equals(pageContext.getParameter(OAWebBeanConstants.EVENT_PARAM))) {
            String rowRef =
                pageContext.getParameter(OAWebBeanConstants.EVENT_SOURCE_ROW_REFERENCE);
            Row currentRow = am.findRowByRef(rowRef);
            currentRow.setAttribute("selector", "Y");
        }

{% endcodeblock %}
 
这样便实现了单选删除功能。

