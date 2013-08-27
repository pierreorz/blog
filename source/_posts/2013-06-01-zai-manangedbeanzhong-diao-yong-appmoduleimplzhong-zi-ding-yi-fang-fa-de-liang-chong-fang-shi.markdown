---
layout: post
title: "在ManagedBean中调用AppModuleImpl中自定义方法的两种方式"
date: 2013-06-01 10:48
comments: true
categories: ADF
tags:
- ManagedBean
- AppModuleImpl
- 调用AM中方法
---

--------------------
平台: Jdev11.1.1.7.0
---
--------------------

1. ****AM****中创建自定义方法sayHello    
{% codeblock lang:java %}
    public void sayHello(String value){
        System.out.println("hello world..."+value);
    }
{% endcodeblock %}
2. 暴露该方法以便在ManagedBean中调用    
![../../../wp-content/uploads/QQ截图20130601103416.jpg](../../../wp-content/uploads/QQ截图20130601103416.jpg)    

3. 从Data Controls中将该方法拖到页面生成Bindings或者手工添加该Bindings。    
![../../../wp-content/uploads/QQ截图20130601103826.jpg](../../../wp-content/uploads/QQ截图20130601103826.jpg)     

4. 为了测试方便，创建了一个inputText，并绑定该inputText的valueChangeListener为ManagedBean中的valueChange方法.    
{% codeblock lang:java %}
    public void valueChange(ValueChangeEvent valueChangeEvent) {
        String newValue = valueChangeEvent.getNewValue().toString();
        //方法一：调用bindings
        BindingContainer bindings = ADFUtils.getBindingContainer();                
        OperationBinding refreshChecklist = bindings.getOperationBinding("sayHello");
        refreshChecklist.getParamsMap().put("value", newValue); 
        refreshChecklist.execute();
        
        //方法二：直接调用AM，通过AM调用该方法
        AppModuleImpl am = (AppModuleImpl)ADFUtils.
		getApplicationModuleForDataControl("AppModuleDataControl");
        am.sayHello(newValue);        
    }
{% endcodeblock %}
   
以上两种方式均可以调用AM中的自定义方法。
