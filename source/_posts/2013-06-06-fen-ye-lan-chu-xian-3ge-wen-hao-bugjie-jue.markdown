---
layout: post
title: "<af:table>分页栏出现3个问号BUG解决"
date: 2013-06-06 01:58
comments: true
categories: ADF
tags:
- ADF
- af:table
- 分页
- Skin
- resource bundle
---

----
平台：Jdeveloper 11.1.1.7.0

----

**BUG描述**

![../../../wp-content/uploads/QQ截图20130606010242.jpg](../../../wp-content/uploads/QQ截图20130606010242.jpg)   

如上图所示，“页”字前后出现三个问号。

**原因分析**

查看LOG文件    
{% codeblock lang:java %}
<RenderingContext> <getTranslatedString> 无法从外观 mySkin.desktop 获取资源关键字 页    
{% endcodeblock %}

可见是RenderingContext类中getTranslatedString方法发生异常。    
查看源代码分析：    

**找到源代码所在位置**：  
  
![../../../wp-content/uploads/QQ截图20130606012628.jpg](../../../wp-content/uploads/QQ截图20130606012628.jpg)

**将JAR包解压得到RenderingContext类，反编译查看**    

![../../../wp-content/uploads/QQ截图20130606012734.jpg](../../../wp-content/uploads/QQ截图20130606012734.jpg)

结果如下**RenderingContext.class**：    
{% codeblock lang:java %}
  public String getTranslatedString(String key)
  {
    if (key == null)
      return null;
    try
    {
      return getSkin().getTranslatedString(getLocaleContext(), key);
    }
    catch (MissingResourceException mre)
    {
      _LOG.severe("CANNOT_GET_RESOURCE_KEY", new String[] { key, getSkin().getId() }); }
    return "???" + key + "???";
  }
{% endcodeblock %}

可知在调用getTranslatedString获取Resource时，发生MissingResourceException异常所以返回了带"???"+Key+"???"字样的字符串。    

**解决方案：**

根据以上分析基本可知出现问号的原因是在获取resourcebundle时，找不到Key为页的resource，所以直接返回了"???" + key + "???"，就出现分页显示出现问号的BUG。    

**方案一：**重写ResourceBundle类，增加KEY值为“页”的资源。
步骤如下：    

- 创建SkinBundle类 extends ListResourceBundle     
{% codeblock lang:java %}
package cn.dengdezhao.bundle
public class SkinBundle extends ListResourceBundle {
    @Override
    public Object[][] getContents() {
        System.out.println("executing...");
        return  new Object[][]{ { "页", "页" } };
    }
}
{% endcodeblock %}

- 创建trinidad-skins.xml文件注册定制化Skin    
{% codeblock lang:xml %}
<?xml version="1.0" encoding="UTF-8"?>
<skins xmlns="http://myfaces.apache.org/trinidad/skin">
    <skin>
        <id>mySkin.desktop</id>
        <family>mySkin</family>
        <extends>skyros-v1.desktop</extends> 
        <render-kit-id>org.apache.myfaces.trinidad.desktop</render-kit-id>
        <style-sheet-name>css/mySkin.css</style-sheet-name>
        <bundle-name>cn.dengdezhao.bundle.SkinBundle</bundle-name>
        <!--<bundle-name>ApplicationBundle</bundle-name>-->
    </skin>
</skins>
{% endcodeblock %}

- 修改trinidad-config.xml文件如下：    
{% codeblock lang:xml %}
<?xml version="1.0" encoding="UTF-8"?>
<trinidad-config xmlns="http://myfaces.apache.org/trinidad/config">
  <skin-family>mySkin</skin-family>
</trinidad-config>
{% endcodeblock %}

- 创建css/mySkin.css文件（内容默认CSS即可）

运行即发现问号消失。

**方案二：**其实原理跟方案一一样，也是通过改resourcebundle实现。resourcebundle支持三种类型，一种是以上的扩展ListResourceBundle类实现，一种是通过配置文件properties实现，另外一种是xliff实现。也可创建一个ApplicationBundle.properties文件，内容如下:    
{% codeblock lang:java %}
页 = 页
{% endcodeblock %}

trinidad-skins.xml里的bundle-name节点设置成ApplicationBundle也可实现同样效果。

定制CSS可参照["定制CSS"](http://www.dengdezhao.cn/blog/2011-06-27/%E5%AE%9A%E5%88%B6adf%E5%BA%94%E7%94%A8%E7%9A%84css/)

