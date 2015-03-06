---
layout: post
title: "Jive Forums admin console 管理员账户密码重置"
date: 2014-12-11 18:05
comments: true
categories: webcenter
tags: 
- webcenter
- discussion
- jive
---
#### <i class="icon-file"></i>   需求：

webcenter集成AD 之后发现Discussion控件用不了，使用weblogic账号也无法进入Jive forums的控制台，但是weblogic控制台可以登录。于是想重置Jive Forums的管理账号密码。

#### <i class="icon-folder-open"></i> 分析：
修改jiveuser表将管理员账户密码更新即可。


#### <i class="icon-pencil"></i> 技术实现：

- 连接到FM_DISCUSSIONS schema，查询jiveuser，发现passwordhash字段值变为了"-"字符，可能是集成AD之后这边的数据被覆盖了，按字段名推测密码应该是hash加密后的密文，于是将weblogic用户的密码通过MD5加密生成密文更新该字段，重启webcenter之后，问题解决。

附：
{% codeblock lang:java %}
//password: Welcome1 
update jiveuser set passwordhash='b56e0b4ea4962283bee762525c2d490f' where username='weblogic'
{% endcodeblock %}

