---
layout: post
title: "关于ApplicatoinModule类型总结"
date: 2013-05-28 15:16
comments: true
categories: ADF
tags:
- Root Application Module
- Nested Application Module
- Shared Application Module
---
--------------------------
平台：Jdev11.1.2.3.0    
----------

####AM的类型####
**1. Root AM**    
**2. Nested AM**    
**3. Shared AM**    

####三种类型AM的比较####

首先谈谈RootAM的数量，RootAM原则上来说越少越好，因为当用户访问工程的时候数据库连接数量会根据RootAM的数量成倍增加极大地增加了数据库的压力。但是很多时候由于业务的复杂性，单一的AM无法满足实际开发的需求，所有的业务逻辑都写在一个AM里极不利于多个团队的协同开发。    
这个时候NestedAM就发挥其作用了，NestedAM是共享其父类RootAM的数据库连接的，所以不会额外增加连接数量，利用NestedAM可以将业务逻辑拆分开来，每个团队都可以使用自己单独的NestedAM，团队之间彼此冲突地可能性大大减少。
再说SharedAM，大型项目中很多模块的功能是通用的，比如LOV或者一些在使用中基本不需要改变的固定配置数据等，这类数据对项目所有访问用户都是固定的，可以使用SharedAM来维护这类型数据。

####三种类型的AM的创建方法####

1. RootAM,默认创建的AM即是RootAM    
2. NestedAM创建方法    
   先创建两个RootAM(deptAm,empAm)，分别拥有VO instance（DeptVO,EmpVO)；    
再创建一个RootAM（rootAM)，双击该rootAM，在Data Model标签页中，展开Application Module Instances节点，如下图所示添加deptAm,empAM为该rootAM的NestedAM。    
![../../../wp-content/uploads/QQ截图20130528145149.jpg](../../../wp-content/uploads/QQ截图20130528145149.jpg)

3. 创建SharedAM的方法更为简单，在上面创建好RootAM之后，右键该Model工程属性，切换到ADF Business Components-Application Module标签页，如下图所示添加SharedAM，SharedAM有两种级别，一种是Application级别的，一种是Session级别的，可根据实际需要将该AM设置为Applicatoin或者是Session级别，二选一。    

![../../../wp-content/uploads/QQ截图20130528150742.jpg](../../../wp-content/uploads/QQ截图20130528150742.jpg)

PS：基于多DB连接的多AM创建可参考前一博文["ADF创建两个AM基于不同的DB连接"](../../../blog/2013-05-23/adfchuang-jian-liang-ge-amji-yu-bu-tong-de-dblian-jie/)
