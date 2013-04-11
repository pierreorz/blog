---
layout: post
title: "JavaMail发送邮件中文乱码"
date: 2013-03-20 16:21
comments: true
categories: java
tags:
- javaMail
- 乱码
---
使用JavaMail发送邮件时，邮件主题以及发件人中有中文字符时出现乱码

邮件正文正常。

尝试对中文字符串进行转码：

{% codeblock lang:java %}
    public String encode(String str) {
        sun.misc.BASE64Encoder enc = new sun.misc.BASE64Encoder();
        try {
            return "=?UTF-8?B?" + enc.encode(str.getBytes("UTF-8")) + "?=";
        } catch (UnsupportedEncodingException e) {
            return "UnsupportedEncodingException";
        }
    }
{% endcodeblock %}

测试发送之后返回`邮件主题`内容仍然是乱码
{% codeblock lang:java %}
=?UTF-8?B?WzIwMTMtMDMtMTld5pyq5qOA5pyq5Lqk5pWw5o2uLOaAu+ijhei9pumXtOaVhemanOi9puWBnOi9
{% endcodeblock %}

通过返回字符串`=?UTF-8?B?`得知已经转码成功，但为啥邮件主题仍然乱码？百思不得其解。然后突然发现之前另外一个邮件发送功能调用相同的代码显示是正常的，推测乱码可能跟主题字符串的内容有关。

经过反复测试，终于找到乱码原因，结果令人大跌眼镜~！

**原来这个版本的JavaMail对邮件主题有长度限制，超过大约15个中文字符就会显示乱码！**
