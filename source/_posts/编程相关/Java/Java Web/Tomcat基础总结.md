---
title: Tomcat基础总结
date: 2021-02-07 11:27:04
tags:
	- Java
	- 服务器
categories:
	- 编程相关
	- Java
	- Java Web
fileName: tomcat-basic-summary
---

## Tomcat

### 目录结构

```
|——bin/  存放启动和关闭Tomcat的脚本文件
|
|——conf/  存放Tomcat服务器的各种配置文件
|  |——server.xml	服务器的主配置文件
|  |——web.xml	定义所有web应用的配置
|  |——tomcat-user.xml	定义了tomcat用户的信息
|
|——lib/  存放所需要的jar文件
|
|——logs/  存放Tomcat的日志文件
|
|——temp/	存放Tomcat运行时产生的临时文件
|
|——webapps/		web应用程序的目录及文件（其下每一个子目录都是一个独立的web应用程序）
|  |——.../WEB-INF	存放Web应用程序的部署描述性文件web.xml（用户不可见）
|     |——classes	存放Servlet和其他有用的类文件
|     |——lib	存放Web应用程序需要用到的jar文件
|     |——web.xml	存放配置与部署信息
|  |——.../index.jsp		首页
|
|——work/	JSP生成的Servlet源文件与字节码文件
|

```



