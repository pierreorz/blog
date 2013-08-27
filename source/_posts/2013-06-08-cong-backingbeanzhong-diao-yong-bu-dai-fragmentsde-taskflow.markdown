---
layout: post
title: "从BackingBean中调用不带fragments的taskflow"
date: 2013-06-08 16:11
comments: true
categories: ADF
tags: 
- ADF
- BackingBean
- fragments
- taskflow
---

有时候需要在backingBean中调用某一taskflow, 可以使用以下代码调用。不过只适用于可单独运行的taskflow，含有page fragments的taskflow不在此范围。    

代码如下：    
{% codeblock lang:java %}
    public String callTaskFlow() {
        FacesContext fctx = FacesContext.getCurrentInstance();
        ControllerContext cc = ControllerContext.getInstance();
        String taskflowId = "btf_task";
        String taskflowDocument = "/WEB-INF/btf_task.xml";
        Map<String, Object> params = new HashMap<String, Object>();
        TaskFlowId tid = new TaskFlowId(taskflowDocument, taskflowId);
        String taskflowURL = cc.getTaskFlowURL(false, tid, params);
        ExtendedRenderKitService erks =
            Service.getRenderKitService(fctx, ExtendedRenderKitService.class);
        StringBuilder sb = new StringBuilder();
        sb.append("window.open(\"" + taskflowURL +
                  "\"").append(",\"_self\");");
        erks.addScript(FacesContext.getCurrentInstance(), sb.toString());
        return null;
    }
{% endcodeblock %}
