---
title: Maven基础使用
date: 2021-02-05 23:30:18
tags:
	- Java
	- maven
categories:
	- 工具使用
fileName: maven
---

## maven基础

### 基本概念

> Maven是Java项目的构建工具，支持从远程库中下载相关jar包等文件，进行依赖管理，并使用xml作为配置文件格式
>
> 类似于Python中的pip等工具

### 特性

* **依赖管理**：支持使用`groupId、artifactId、version`组成的`Coordination`（坐标）唯一标识一个依赖，而任何基于 Maven 构建的项目自身也必须定义这三项属性，生成的包可以是 Jar 包，也可以是 war 包或者 jar 包
* **多模块构建**：可以定义父模块与子模块，完成软件架构的分离（如 dao service controller 三层架构）
* **一致的目录结构**：只要是使用Maven构建的项目，在不同的IDE中项目结构是一致的
* **一致的构建模型与插件的机制**：如配置tomcat、jetty等插件在不同环境下是一样的

### maven项目目录结构

```
|——src/main/	
|	   |——java		项目Java源代码
|	   |——resources	项目资源
|
|——src/test/
|	   |——java		项目测试类（JUnit）
|	   |——resources	测试资源
|
|——pom.xml	配置文件
```

### maven设置

> maven的配置文件为`path/to/maven/conf/settings.xml`，大部分配置都在该文件中修改

#### 修改默认仓库的位置

修改`<localRepository>`标签

```
<!-- 本地仓库地址，存放jar包的位置 -->
<localRepository>/path/to/local/repo</localRepository>
```

#### 修改国内镜像

在相同的配置文件中更改`<mirrors>`中加载国内镜像

### maven仓库

> 对于 Maven 来说， 仓库只分为两类： 本地仓库和远程仓库
>
> 当 Maven 根据坐标寻找构件时，首先会查看本地仓库，如果本地仓库存在，则直接使用； 如果本地没有，Maven 就会去远程仓库查找；如果都没有则报错

### maven命令格式

执行`plugin-name`插件的`goal-name`目标

```
mvn [plugin-name]:[goal-name]
```

命令参数：以`-D`开头传入属性参数，以`-P`开头使用指定的Profile配置

#### 使用-D

```
mvn exec:java -Dexec.mainClass="类A"		# 执行类A
```

#### 使用-P

使用-P选择打包的对应环境，以下选择了test环境

```
mvn package t -Ptest -Dmaven.test.skip=true		# 打包测试环境并跳过maven测试
```

### maven常用命令

| 命令                | 功能                                                    |
| ------------------- | ------------------------------------------------------- |
| mvn -version        | 显示版本信息                                            |
| mvn clean           | 清理项目生产的临时文件，—般是模块下的target目录         |
| mvn compile         | 编译源代码，一般编译模块下的src/main/java目录           |
| mvn package         | 项目打包工具，会在模块下的target目录生成jar或war等文件  |
| mvn test            | 测试命令，或执行src/test/javal下junit的测试用例         |
| mvn install         | 将打包的jar/war文件复制到你的本地仓库中，供其他模块使用 |
| mvn deploy          | 将打包的文件发布到远程参考,提供其他人员进行下载依赖     |
| mvn dependency:tree | 打印出项目的整个依赖树                                  |
| mvn tomcat7:run     | 在tomcat容器中运行web应用                               |
| mvn jetty:run       | 调用Jetty插件的 Run目标在Jetty Servlet容器中启动web应用 |

### pom文件常用标签





## IDEA中使用maven

### 配置maven

在`Setting for New Project`中选择下载的maven目录以及对应的`settings.xml`配置文件，使得每次使用IDEA创建maven项目使用我们下载的而不是IDEA系统自带的。

### 创建maven项目

新建项目选择Maven，如果是创建普通项目，选择`quickstart`，创建Web项目选择`webapp`。

![](http://cdn.ziyedy.top/Maven/maven%E9%A1%B9%E7%9B%AE%E5%90%AF%E5%8A%A8.png)

### 设置运行方式

在IDEA右上角选择运行Maven项目的命令，如下即构建了Maven 的编译命令，如果是使用Maven构建的Web项目的话也可以使用Maven中相应的使用服务器运行的方法。

![](http://cdn.ziyedy.top/Maven/%E8%AE%BE%E7%BD%AEmaven%E6%89%A7%E8%A1%8C%E5%91%BD%E4%BB%A4.png)





## maven常见bug

### bug1：IDEA构建项目失败

#### 报错

```
Could not transfer metadata org.apache.maven.plugins:maven-archetype-plugin/maven-metadata.xml from/to alimaven (http://maven.aliyun.com/nexus/content/repositories/central/): Transfer failed for http://maven.aliyun.com/nexus/content/repositories/central/o
```

#### 解决

在设置中为Maven启动加入如下命令

![](http://cdn.ziyedy.top/Maven/bug1.png)

```
-Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true
```

### bug2：插件出错

#### 报错

```
Cannot resolve plugin org.apache.maven.plugins:maven-clean-plugin:2.5 Maven
```

#### 解决

如图所示，删除对应出错的插件，然后点击左上角重新构建即可

<img src="http://cdn.ziyedy.top/Maven/bug2.png" style="zoom:67%;" />

### bug3：Could not transfer artifact ...

在导入的虚拟机选项中插入如下代码，忽略SSL证书的验证

```
-Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true
```

<img src="http://cdn.ziyedy.top/Maven/bug3.png" style="zoom: 50%;" />

如果还报错的话，则将maven本地包中结尾为`.lastUpdate`的文件删除，输出该类文件需要使用相关命令或脚本，如下为Windows下删除方法。

```
cd maven仓库
for /r %i in (*.lastUpdated) do del %i
```





