---
title: SSM整合
date: 2021-05-02 15:19:52
tags:
    - SSM
categories:
    - Java
	- 框架
fileName: SSM-combine
---

SSM 整合，需要把对象交给容器管理， 让容器去创建项目中要使用的java对象。 现在有两个容器。 

第一个是Spring容器： Spring容器是管理service 和 dao等对象的。  是业务层对象的容器。

第二个是SpringMVC容器：管理控制器对象的。   是视图层对象。



SSM整合就是把对象交给容器管理。  两个容器共存。 各自负责管理不同的对象。 把对象声明到配置文件中，让两个容器创建对象。  spring创建service，dao； springmvc创建controller。



