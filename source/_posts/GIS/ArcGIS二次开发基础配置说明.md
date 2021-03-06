---
title: ArcGIS二次开发基础配置说明
date: 2019-04-28 22:48:42
tags:
    - ArcGIS
categories:
    - GIS
fileName: ArcGIS_dev
---
很早就听说过ArcGIS二次开发各种麻烦所以也一直不打算去做这一块（电脑里装的是VS2017，但ArcEngine老一些的版本不支持这个版本，新的版本又找不到，实在是不想再装一个新的VS），但这学期由于一个小实习的需要，不得不稍微学一下，也算完成任务吧。仔细想想稍微了解下ArcGIS二次开发也不坏，毕竟我还是个学GIS的。。。
## 开发环境安装
首先想明确的是，一个电脑里装两个版本的VS2017正常情况下是不会有问题的，之前网上总有些人说这样安装会失败，也许是他们自己的问题吧，反正经过尝试本人是没发现任何问题。同时AE10.2推荐的VS版本是2010或2012，更高的版本会报错，所以感觉极端stupid。
1. 安装AE之前必须保证先安装VS，而VS的安装包很好找，推荐一个公众号“软件安装管家”，里面应有尽有。
2. 之后安装ArcGIS Desktop，ArcGIS反正我是感觉是一个水很深的软件，同样推荐上面那个公众号，跟着步骤安装。
3. 最后开始安装AE，[下载点我,p2f1](https://pan.baidu.com/s/1k5cg94K70yXwLO2ZX0coBg)，注意选择对应VS版本的AE安装。
    1. 对安装包进行解压，解压Part1然后在解压过程中会弹出对话框加上Part2即可
    2. 解压后点开解压文件，打开ESRI.exe文件安装相应文件即可
    3. 打开相应VS版本，查看有没有ArcGIS开发组件即可。

## 基本事项
本人也没有具体去系统学过所谓ArcGIS二次开发，因为大部分时候的确用不上这些，现在只是所谓面向作业编程。这次作业实现了delaunay三角网以及相应图形的合并，由于老师有样例代码所以很简单。不过还是得吐槽一句，给代码不把框架说清楚，给的接口参考文件说明只有一个名词，比如某接口中，方法Area边上写了个面积，难道我不知道这个单词是面积的意思？关键是这方法怎么调用吧。百度，OK，ArcGIS二次开发基本没人做，然后就只能去查帮助文档了，这本身无可厚非，但说白了，这么个搞清楚架构、理解老师要干啥花三个小时，编程一刻钟的作业，真的蠢。

### 帮助文档
ArcGIS二次开发做的人不多，网上基本上找不到有参考意义的教程（当然某些论坛还是不错的），而这玩意又以接口众多出名（几乎是面向接口编程），所以帮助文档就很重要了。
一切安装妥当后，在**开始->ArcGIS->ArcObjects help**中打开就能找到，之后在其中选择相应接口之类的查看即可。各种参数和不同语言的不同用法写的还是很清楚的。

### 文件迁移设置变更
由于是ArcGIS二次开发，所以需要调用外部的ArcMap程序。在代码迁移的时候，由于不同终端上ArcMap存储的文件位置不一致，所以要手动进行更改。
主要步骤是
右击项目名称，选择最下面的“属性”，并且选择属性菜单栏中的“调试”，在启动操作中选择“启动外部程序”并在其中将文件路径更改为现在运行终端中的相应文件路径即可。具体如下所示：
![](img/BlogImg/arcgis1/1.jpg)

### 常见错误处理
编译调试运行时如果出现了诸如“无法注册程序集 .dll- 拒绝访问。请确保您正在以管理员身份运行应用程序。对注册表项“...”的访问被拒绝”的错误，可以进行如下操作处理。
WIN+R，运行对话框中输入gpedit.msc，开启组策略，然后一步步地在“计算机配置”-“Windows 设置”-“安全设置”-“本地策略”-“安全选项”，找到右侧的“用户账户控制：以管理员批准模式运行所有管理员”这个项，你会看到这个项默认是启用的，把它设成禁用。如下图
![](img/BlogImg/arcgis1/2.png)
之后重启电脑即可。