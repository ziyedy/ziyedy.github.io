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

# maven基础

## 基本概念

> Maven是Java项目的构建工具，支持从远程库中下载相关jar包等文件，进行依赖管理，并使用xml作为配置文件格式

类似于Python中的pip等工具

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

## maven常用配置

> maven的配置文件为`path/to/maven/conf/settings.xml`，大部分配置都在该文件中修改

setting.xml主要用于配置maven的运行环境等一系列通用的属性，是全局级别的配置文件

### 修改默认仓库的位置

修改`<localRepository>`标签

```
<!-- 本地仓库地址，存放jar包的位置 -->
<localRepository>/path/to/local/repo</localRepository>
```

### 修改国内镜像

在相同的配置文件中更改`<mirrors>`中加载国内镜像

## maven命令

### maven命令常用格式

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

大部分都被IDEA集成了，在maven侧边栏点击使用即可

其中`mvn install`需要注意，*如果本地有两个项目，其中一个需要依赖另一个*，则需要运行该命令，否则无法找到相应的文件或接口

| 命令                | 功能                                                    |
| ------------------- | ------------------------------------------------------- |
| mvn -version        | 显示版本信息                                            |
| mvn clean           | 清理项目生产的临时文件，—般是模块下的target目录         |
| mvn compile         | 编译源代码，一般编译模块下的src/main/java目录           |
| mvn package         | 项目打包工具，会在模块下的target目录生成jar或war等文件  |
| mvn test            | 测试命令，或执行src/test/javal下junit的测试用例         |
| **mvn install**     | 将打包的jar/war文件复制到你的本地仓库中，供其他模块使用 |
| mvn deploy          | 将打包的文件发布到远程仓库，提供其他人员进行下载依赖    |
| mvn dependency:tree | 打印出项目的整个依赖树                                  |
| mvn tomcat7:run     | 在tomcat容器中运行web应用                               |
| mvn jetty:run       | 调用Jetty插件的 Run目标在Jetty Servlet容器中启动web应用 |

# 依赖管理与pom文件

## 依赖管理

### maven仓库

> 对于 Maven 来说， 仓库分为两类： 本地仓库和远程仓库（远程仓库可分为中央仓库与私服）

当 Maven 根据坐标寻找构件时，首先会查看本地仓库，如果本地仓库存在，则直接使用； 如果本地没有，Maven 就会去远程仓库查找；如果都没有则报错

* 中央仓库：默认地址为http://repo1.maven.org/maven2/（可以设置镜像，见maven配置），提供一个类似于ES的高效索引机制
* 私服：一般是公司搭建的一个远程中央仓库代理

### 依赖定位

找到所需要的依赖

| 元素       | 描述                                                         | e备注                                                        |
| :--------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| groupId    | 定义当前模块隶属的**实际Maven项目**, 表示方式与Java包类似    | **groupId**不应直接对应项目隶属的公司/组织(一个公司/组织下可能会有很多的项目). |
| artifactId | 定义实际项目中的一个**Maven模块**                            | 推荐使用项目名作为**artifactId**前缀, 如:**commons-lang3**以**commons**作为前缀(因为Maven打包默认以`artifactId`作为前缀) |
| version    | 定义当前项目所处版本(如*1.0-SNAPSHOT*、*4.2.7.RELEASE*、*1.2.15*、*14.0.1-h-3* 等) | Maven版本号定义约定: ***<主版本>\***.***<次版本>\***.*<增量版本>*-*<里程碑版本>* |
| packaging  | 定义Maven项目打包方式, 通常**打包方式与所生成构件扩展名对应** | 有*jar*(默认)、*war*、*pom*、*maven-plugin*等.               |
| classifier | 用来帮助定义构建输出的一些**附属构件**(如*javadoc*、*sources*) | 不能直接定义项目的**classifier**(因为**附属构件不是由项目默认生成**, 须有附加插件的帮助) |

 ### 依赖传递

> 自动加载引入的依赖包的依赖

## pom文件详解

> pom.xml主要描述了项目的maven坐标，依赖关系，开发者需要遵循的规则，缺陷管理系统，组织和licenses，以及其他所有的项目相关因素，是项目级别的配置文件

### 基础配置

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0http://maven.apache.org/xsd/maven-4.0.0.xsd">
 
	<!-- 模型版本。maven2.0必须是这样写，现在是maven2唯一支持的版本 -->
	<modelVersion>4.0.0</modelVersion>
 
	<!-- 公司或者组织的唯一标志，并且配置时生成的路径也是由此生成， 如com.winner.trade，maven会将该项目打成的jar包放本地路径：/com/winner/trade -->
	<groupId>com.winner.trade</groupId>
 
	<!-- 本项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 -->
	<artifactId>trade-core</artifactId>
 
	<!-- 本项目目前所处的版本号 -->
	<version>1.0.0-SNAPSHOT</version>
 
	<!-- 打包的机制，如pom,jar, maven-plugin, ejb, war, ear, rar, par，默认为jar -->
	<packaging>jar</packaging>
 
	<!-- 帮助定义构件输出的一些附属构件,附属构件与主构件对应，有时候需要加上classifier才能唯一的确定该构件 不能直接定义项目的classifer,因为附属构件不是项目直接默认生成的，而是由附加的插件帮助生成的 -->
	<classifier>...</classifier>
 
	<!-- 定义本项目的依赖关系 -->
	<dependencies>
 
		<!-- 每个dependency都对应这一个jar包 -->
		<dependency>
 
			<!--一般情况下，maven是通过groupId、artifactId、version这三个元素值（俗称坐标）来检索该构件， 然后引入你的工程。如果别人想引用你现在开发的这个项目（前提是已开发完毕并发布到了远程仓库），--> 
			<!--就需要在他的pom文件中新建一个dependency节点，将本项目的groupId、artifactId、version写入， maven就会把你上传的jar包下载到他的本地 -->
			<groupId>com.winner.trade</groupId>
			<artifactId>trade-test</artifactId>
			<version>1.0.0-SNAPSHOT</version>
 
			<!-- maven认为，程序对外部的依赖会随着程序的所处阶段和应用场景而变化，所以maven中的依赖关系有作用域(scope)的限制。 -->
			<!--scope包含如下的取值：compile（编译范围）、provided（已提供范围）、runtime（运行时范围）、test（测试范围）、system（系统范围） -->
			<scope>test</scope>
 
			<!-- 设置指依赖是否可选，默认为false,即子项目默认都继承:为true,则子项目必需显示的引入，与dependencyManagement里定义的依赖类似  -->
			<optional>false</optional>
 
			<!-- 屏蔽依赖关系。 比如项目中使用的libA依赖某个库的1.0版，libB依赖某个库的2.0版，现在想统一使用2.0版，就应该屏蔽掉对1.0版的依赖 -->
			<exclusions>
				<exclusion>
					<groupId>org.slf4j</groupId>
					<artifactId>slf4j-api</artifactId>
				</exclusion>
			</exclusions>
 
		</dependency>
 
	</dependencies>
 
	<!-- 为pom定义一些常量，在pom中的其它地方可以直接引用 使用方式 如下 ：${file.encoding} -->
	<properties>
		<file.encoding>UTF-8</file.encoding>
		<java.source.version>1.5</java.source.version>
		<java.target.version>1.5</java.target.version>
	</properties>
 
	...
</project>
```

### modules

https://www.cnblogs.com/youzhibing/p/5427130.html

### parents

## SNAPSHOT

Maven 中的仓库分为两种，SNAPSHOT 快照仓库和 Release 发布仓库。

> 开发、测试阶段使用SNAPSHOT，上线前修改为Release
>
> client变更需要升级版本，线下统一使用快照包(后缀-SNAPSHOT，示例:1.0-SNAPSHOT) ，线上使用正式包（示例:1.0）

### 两者区别

* SNAPSHOT快照仓库：用于保存开发中的不稳定版本。
  * 快照版本在`mvn deploy`时会自动发布到快照版本库，并自动覆盖老版本。同时在使用快照版本时，maven会自动从服务器上下载最新的快照版本
* Release发布仓库：保存正式的发行版本。
  * 正式版本在`mvn deploy`时会自动发布到正式版本库，但在使用时，编译打包在本地已存在该版本模块时不会自动去服务器下载最新的版本



# 工具使用与常见问题

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





