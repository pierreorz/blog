---
layout: post
title: "getRowAtRangeIndex(int)方法的局限性"
date: 2013-04-16 14:42
comments: true
categories: ADF
tags: 
- ADF
- getRowAtRangeIndex
- RowSetIterator
---
导出VO中的数据到EXCEL中时，循环VO中的Row，写入HSSFRow，是通过getRowAtRangeIndex(int)方法循环取Row，代码如下：    
{% codeblock lang:java %}
 ViewObject vo8=am.findViewObject("ChReport1VO1");
 if(vo8!=null)
 {
      int rowcount8=vo8.getRowCount();
      for(int i=0;i<rowcount8;i++)
      {
           Row vo_row8=vo8.getRowAtRangeIndex(i);
		   //...TODO
      }
 }
{% endcodeblock %}

这样如果VO里有300条数据，但是pageRange设置为100，这里其实只能取到第一页的100条数据，想要导出所有300条数据则需要一页一页导出，很麻烦。于是考虑不用getRowAtRangeIndex方法取ROW数据,改用RowSetIterator。

{% codeblock lang:java %}
ViewObject vo8=am.findViewObject("ChReport1VO1");
if(vo8!=null)
{
     int rowcount8=vo8.getRowCount();
     RowSetIterator rowSetIter=vo8.createRowSetIterator(null);
              
     for(int i=0;i<rowcount8;i++)
     {
           Row vo_row8=rowSetIter.next();
		   //...TODO
     }
}
{% endcodeblock lang:java %}

getRowAtRangeIndex方法只能通过index取出Range中的数据,但是RowSetIterator则是对VO中所有RowSet遍历。
