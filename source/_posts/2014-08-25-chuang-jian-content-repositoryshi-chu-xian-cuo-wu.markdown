---
layout: post
title: "创建Content Repository时出现错误"
date: 2014-08-25 18:02
comments: true
categories: webcenter
tags: 
- content repository
---
#### <i class="icon-file"></i>**创建Content Repository时出现错误：**

> **NOTE:**
> 
> - Unable to load content server metadata model using GET_DOC_METADATA_INFO.
> - Permission denied. Address 'XX.XX.XX.XX' is not an allowable remote socket address


#### <i class="icon-file"></i> **解决方法:**



> -  <i class="icon-pencil"></i> 找到config.cfg     
`/u02/app/oraucm/fmw_home/user_projects/domains/ecm_domain/ucm/cs/config/config.cfg`

> - <i class="icon-pencil"></i>在SocketHostAddressSecurityFilter值加上`|*.*.*.*`
SocketHostAddressSecurityFilter=127.0.0.1|192.168.15.163|`*.*.*.*`即可
