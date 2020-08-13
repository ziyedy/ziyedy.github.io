---
title: OSM数据基本操作
date: 2019-02-23 23:26:10
tags:
    - GIS
categories:
    - GIS
	- 开源GIS
fileName: osm_data_process
---
## .osm文件在ArcGis中打开

.osm有一万种方式可以转化为ArcGis支持的格式，但本人有些强迫症就是想让ArcGis能够直接打开.osm文件，好在官方已经提供了插件，不过只支持ArcGis 10.6的版本，所以版本滞后的小伙伴请先进行更新。
更新完之后，请移步[传送门](https://www.esri.com/en-us/arcgis/products/arcgis-editor-for-openstreetmap),里面只有一个选项，相信诸位不会弄错。之后，解压安装包，选择电脑相应位数安装即可，十分方便。安装完成后，打开arcgis后，在ArcToolbox中你会发现新增了一个**OSM Toolbox**，其中内容如下，接下来根据英文与帮助文档，就可以很轻松地进行相应的处理了。在这儿，我们使用*Load OSM File*，选择文件路径与数据库后，即可打开OSM文件，效果如下。
<img src="http://cdn.ziyedy.top/image/GIS/OSM%E6%95%B0%E6%8D%AE%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9Carcgis.png" alt="OSM工具" style="zoom:80%;" />
<img src="http://cdn.ziyedy.top/image/GIS/OSM%E6%95%B0%E6%8D%AE%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9Carcgis1.png" alt="生成的查询代码" style="zoom:67%;" />

## Overpass Turbo
### Overview
**Overpass Turbo**[传送门](http://overpass-turbo.eu/)我认为是提供了一个用于查询与分析OSM数据的平台吧，它的较为标准的说明以及功能描述如下：
A web-based data filtering tool for OpenStreetMap. With overpass turbo you can run Overpass API queries and analyse the resulting OSM data interactively on a map. There is an integrated Wizard which makes creating queries super easy.
下面简单记录一下其中常用到的操作，有些命名不是其文档的标准命名，反正领会其意即可。如下是Overpass Turbo的整个界面，其中**红色笔**圈出区域内为工具条（toolbar），**黄色笔**圈出的区域内为查询代码的编写处，其右侧相信大家都能猜到了，就是用于显示所查询内容的部分，其中显示方式的话可以在**绿色笔**圈出的区域内调整。

![](http://cdn.ziyedy.top/image/GIS/OSM%E6%95%B0%E6%8D%AE%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9Ctotal.jpg)
该界面的工具条很简单，没有什么选项，都是一些常见的英文因此简单说一说吧。**Run**就是运行查询代码并在右侧区域进行结果显示，刚进去Overpass Turbo时会有一个例子代码如上图，`node`自然代表一个点要素，`amenity=*****`同OSM中的标签进行查找，很好理解。**Share**自然就是分享；**Export**是下载数据；**Wizard**同其意，作为搜索向导简化查找过程；**Load**则可以载入包括示例之类的东西，其余三个都同其字面意思，在此不做赘述。

### Example
下面简单做一次使用。
首先点击**Wizard**，在其对话框中输入*bus route*，完成后可见左侧代码区自动生成了查询代码，如下图，其上方注释表明这是由wizard生成的。接下来点击**Run**（若在Wizard中已经运行过请忽略这一步），可见其查找结果（查找的大致范围为武昌地区），如下。

<img src="http://cdn.ziyedy.top/image/GIS/OSM%E6%95%B0%E6%8D%AE%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9Cquery.png">
<img src="http://cdn.ziyedy.top/image/GIS/OSM%E6%95%B0%E6%8D%AE%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9Cmap1.png" style="zoom: 80%;" >
<img src="http://cdn.ziyedy.top/image/GIS/OSM%E6%95%B0%E6%8D%AE%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9Ccode.png" style="zoom: 80%;" >



接下来如果想把查找的数据下载的话可以点击**Export**了，如下对话框，选择数据格式之后等内容之后即可进行下载。至于数据咋用就随你咯。当然如果数据量过大时，地图渲染可能需要很多时间，可以耐心等待一下（平台也会有提示）
<img src="http://cdn.ziyedy.top/image/GIS/OSM%E6%95%B0%E6%8D%AE%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9Cexports.png">