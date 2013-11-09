---
layout: post
title: "如何在页面碎片加载前调用逻辑"
date: 2013-09-23 18:32
comments: true
categories: ADF
tags:
- ADF
- JSFF
- RegionController
- Region
---

- 平台:Jdeveloper 12c

- 需求：在JSPX页面加载前如果需要调用逻辑可以通过重写PhaseListener实现，如果在JSFF页面碎片加载前（也即是region加载前）调用应该如何实现？

**实现方法**

重写RegionController方法可以达到以上需求。

- 重写 RegionController类，在refreshRegion方法中加入需要调用的逻辑即可在页面碎片加载之前调用。

{% codeblock lang:java %}
package cn.dengdezhao;
import oracle.adf.model.RegionContext;
import oracle.adf.model.RegionController;
public class myController implements RegionController {
    public myController() {
        super();
    }
    @Override
    public boolean refreshRegion(RegionContext regionContext) {
        // TODO Implement this method
        int flag=regionContext.getRefreshFlag();
        System.out.println("refreshRegion...");      
        
        return false;
    }
    @Override
    public boolean validateRegion(RegionContext regionContext) {
        // TODO Implement this method
        
        return false;
    }
    @Override
    public boolean isRegionViewable(RegionContext regionContext) {
        // TODO Implement this method
        
        return false;
    }
    @Override
    public String getName() {
        // TODO Implement this method
        
        return null;
    }
}
{% endcodeblock %}

- 配置页面碎片pagedef页

{% codeblock lang:xml %}
<?xml version="1.0" encoding="UTF-8" ?>
<pageDefinition xmlns="http://xmlns.oracle.com/adfm/uimodel" version="12.1.2.66.68" id="iteratorInputPageDef"
                ControllerClass="cn.dengdezhao.myController"
                Package="cn.dengdezhao.pageDefs">
  <parameters/>
  <executables>
    <variableIterator id="variables"/>
  </executables>
  <bindings/>
</pageDefinition>
{% endcodeblock %}

**结论**

通过重写regionController类可以实现在页面碎片加载前调用POPUP提示等之类的需求。
