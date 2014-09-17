---
layout: post
title: "webcenter portal调用document service"
date: 2014-08-26 18:46
comments: true
categories: webcenter
---
**平台:** webcenter 1.8

#### <i class="icon-file"></i> Webcenter Contents端配置
> **步骤:**

> - 进入webcenter contents http://192.168.15.251:16210/cs/idcplg
> - 展开administration目录
> - Admin Server节点
> - Component Manager 里
> - 开启以下特性`FrameworkFolders`(原`Floder_g`，已被`FrameworkFolders`替代)，`WebCenterConfigure`, `DynamicConverter`
> - 重启UCM

#### <i class="icon-file"></i>  Webcenter Portal端配置

> **进入Portal管理界面:**

> - 切换到Portals标签
> - Tools and Services菜单里
> - 设置Documents服务为enabled即可
