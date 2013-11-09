---
layout: post
title: "控件af:iterator以及af:forEach的对比"
date: 2013-09-23 18:13
comments: true
categories: ADF
tags:
- ADF
- iterator
- forEach
---

- 平台:Jdeveloper 12c

- 需求：对Collection类型遍历。MB中定义两个List，如何利用iterator&foreach控件将List内容显示到页面。

**页面如下**

分别绑定iterBean中的两个集合类型变量，将其中的值取出来。

{% codeblock lang:java %}
<af:iterator id="i1" var="each" value="#{iterBean.list}" varStatus="row">
    <af:panelBox text="#{each}" id="pb1">
        <f:facet name="toolbar"/>
        <af:inputText label="Values" id="it1" value="#{iterBean.values[row.index]}"/>
    </af:panelBox>
</af:iterator>
<af:forEach items="#{iterBean.testList}"  var="each" varStatus="row">
  <af:panelBox text="#{each.title}" id="pb2">
    <af:outputText value="#{row.index} #{each.title}" id="ot1"/>
    </af:panelBox>
</af:forEach>
{% endcodeblock %}

**BackingBean内容如下：**

{% codeblock lang:java %}
package cn.dengdezhao;
import java.util.ArrayList;
import java.util.List;
public class IteratorBean {
    List list = new ArrayList();
    String[] values=new String[]{"1","2","3"};
    List<contentList> testList=new ArrayList<contentList>();

    public void setTestList(List<contentList> testList) {
        System.out.println("setTestList...");
        this.testList = testList;
    }
    public List<contentList> getTestList() {
        System.out.println("getTestList...");

        return testList;
    }
    public void setValues(String[] values) {
        this.values = values;
    }
    public String[] getValues() {
        return values;
    }
    public IteratorBean(){
        list.add("A");
        list.add("B");
        list.add("C");
        contentList a=new contentList();
        a.setTitle("aaa");
        testList.add(a);
        contentList b=new contentList();
        b.setTitle("bbb");
        testList.add(b);
        contentList c=new contentList();
        c.setTitle("ccc");
        testList.add(c);
        System.out.println("init...");
    }
    public void setList(List list) {
        System.out.println("setList ...");
        this.list = list;
    }
    public List getList() {
        System.out.println("getList...");
        return list;
    }
}

public class contentList {
    private String title;

    public void setTitle(String title) {
        this.title = title;
    }

    public String getTitle() {
        return title;
    }

}
{% endcodeblock %}    

**测试结果**

<af:iterator>只调用了一次get方法；
<af:forEach>却调用了9次get方法（根据容器中数量递增）；

**结论**

<af:iterator>跟<af:forEach>控件都可以对容器类型遍历，只不过iterator是一次性取出容器内容，而forEach是多次取出。两控件都支持下标定位。
