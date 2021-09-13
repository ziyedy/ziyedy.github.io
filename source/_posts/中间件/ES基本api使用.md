---
title: ES基本api使用
date: 2021-07-04 11:14:15
tags:
categories:
fileName:
---







搜索引擎解决方案

- 一个分布式的实时文档存储，每个字段可以被索引与搜索
- 一个分布式实时分析搜索引擎
- 能胜任上百个服务节点的扩展，并支持 PB 级别的结构化或者非结构化数据
- 基于Lucene，隐藏复杂性，提供简单易用的Restful API接口、Java API接口（还有其他语言的API接口）。ES是一个可高度扩展的全文搜索和分析引擎。它能够快速地、近乎实时地存储、查询和分析大量数据。

./elasticsearch  # 启动ES

# 索引操作

## HTTP服务请求

### 创建索引

向 ES 服务器发 `PUT` 请求：

```
http://127.0.0.1:9200/shopping
```

重复添加索引，会返回错误信息

### 查看所有索引

向 ES 服务器发 `GET` 请求 :

```
http://127.0.0.1:9200/_cat/indices?v
```

### 查看单个索引

向 ES 服务器发 `GET` 请求：查看名为shopping的索引

```
http://127.0.0.1:9200/shopping
```

### 删除索引

向 ES 服务器发 `DELETE` 请求：

```
http://127.0.0.1:9200/shopping
```

## Java操作

```
// 创建 ES 客户端
RestHighLevelClient esClient = new RestHighLevelClient(
		RestClient.builder(new HttpHost("localhost", 9200, "http"))
);

// 关闭 ES 客户端
esClient.close();
```





### 创建索引

```java
// 创建索引
CreateIndexRequest request = new CreateIndexRequest("student");
CreateIndexResponse createIndexResponse = esClient.indices().create(request, RequestOptions.DEFAULT);
// 返回响应状态
boolean acknowledged = createIndexResponse.isAcknowledged();
```



```java
GetIndexRequest request = new GetIndexRequest("student");
GetIndexResponse response = esClient.indices().get(request, RequestOptions.DEFAULT);
```







# 文档操作

## HTTP服务请求

### 创建文档

向 ES 服务器发 POST 请求：

```
http://127.0.0.1:9200/shopping/_doc
```



唯一标识id默认会随机生成，如果想要自定义唯一性标识，需要在创建时指定：

```
http://127.0.0.1:9200/shopping/_doc/1
```

### 查看文档

向 ES 服务器发 GET 请求 :

```
http://127.0.0.1:9200/shopping/_doc/1
```



### 修改文档

请求体发生变化

向 ES 服务器发 POST 请求：

```
http://127.0.0.1:9200/shopping/_doc/1
```



### 修改字段

向 ES 服务器发 POST 请求 :

```
http://127.0.0.1:9200/shopping/_update/1
```

请求体内容

```json
{
	"doc": {
  	"price":3000.00
	}
}
```

### 删除文档

#### 根据id删除

根据文档唯一性标识进行删除

向 ES 服务器发 DELETE 请求 :

```
http://127.0.0.1:9200/shopping/_doc/1
```

#### 条件删除

向 ES 服务器发 POST 请求

```
http://127.0.0.1:9200/shopping/_delete_by_query
```

请求体内容

```json
{
  "query": {
    "match": {
      "price": 4000.0
    }
  }
}
```



# 映射操作

映射类似传统关系型数据库中的表结构

## HTTP请求

### 创建映射

向 ES 服务器发 PUT 请求，如下创建名为student的映射

```
http://127.0.0.1:9200/student/_mapping
```



```json
{
  "properties": {
   "name":{
     "type": "text",
     "index": true
	}, 
   "sex":{
     "type": "text",
     "index": false
  },
   "age":{
     "type": "long",
     "index": false
	}
 }
}
```

* 字段名：类似于关系表中的字段
* type：类型，ES中主要类型有如下几个：
  * String类型：分为两种，text（可分词）；keyword（不可分词）
  * Numerical：数值类型
  * Date：日期类型
  * Array：数组类型
  * Object：对象
* Index：是否进行索引，默认为true，**为false时不能用来搜索**
* store：是否独立存储，默认为false。独立存储的字段解析快很多，但也会占用更多空间

analyzer：分词器

### 查看映射

向 ES 服务器发 GET 请求 :

```
http://127.0.0.1:9200/student/_mapping
```

### 索引关联映射

向 ES 服务器发 PUT 请求

```
http://127.0.0.1:9200/student1
```

请求体中填写相关映射



# 查询

## HTTP请求

### 普通查询

Elasticsearch 提供了基于 JSON 提供完整的查询 DSL 来定义查询

```
http://127.0.0.1:9200/student/_search
```



发送请求体，其中`query"`可以视为一个查询对象，里面可以有很多不同的查询类型

```json
{
  "query": {
    "match_all": {}
  }
}
```



| 查询关键字  | 功能                                                         |
| ----------- | ------------------------------------------------------------ |
| match_all   | 查询所有文档                                                 |
| match       | 把查询条件进行分词，然后进行查询，多个词条之间是 or 的关系   |
| multi_match | 与 match 类似，可以在多个字段中查询                          |
| term        | 精确的关键词匹配查询，不对查询条件进行分词                   |
| terms       | 和 term 查询一样，但它允许指定多值进行匹配（Java代码中差是否in List） |
|             |                                                              |



查询结果：

```json
{
	"took【查询花费时间，单位毫秒】" : 1116, 
  "timed_out【是否超时】" : false, 
  "_shards【分片信息】" : {
		"total【总数】" : 1, 
    "successful【成功】" : 1, 
    "skipped【忽略】" : 0, 
    "failed【失败】" : 0
  },
  "hits【搜索命中结果】" : {
    "total"【搜索条件匹配的文档总数】: {
      "value"【总命中计数的值】: 3,
      "relation"【计数规则】: "eq" # eq 表示计数准确， gte 表示计数不准确
  	},
    "max_score【匹配度分值】" : 1.0, 
    "hits【命中结果集合】" : [
      {
        # 结果item
      }
		]
	}
}
```



"query":这里的query代表一个查询对象，里面可以有不同的查询属性 

"match_all":查询类型，例如:match_all(代表查询所有)， match，term ， range 等等 {查询条件}:查询条件会根据类型的不同，写法也有差异



### 组合查询

`bool`把各种其它查询通过`must`（必须 ）、`must_not`（必须不）、`should`（应该）的方式进行组合

查询体

```json
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            # 条件
          }
        }
      ],
      "must_not": [
        {
          "match": {
            # 条件
          }
        }
      ],
      "should": [
        {
          "match": {
            # 条件
          }
        }
      ]
    }
  }
}
```



### 范围查询

使用 `range` 关键字，进行范围查询。

可以使用如 gt（大于）、gte（大于等于）、lt（小于）、lte（小于等于）

```json
{
  "query": {
    "range": {
      "age": {
        "gte": 30,
        "lte": 50
      }
    }
  }
}
```



### 模糊查询

返回包含与搜索字词相似的字词的文档

为了找到相似的术语，fuzzy 查询会在指定的**编辑距离**（将一个术语转换为另一个术语所需的一个字符更改的次数）内创建一组搜索词的所有可能的变体 或扩展。然后查询返回每个扩展的完全匹配

```json
{
  "query": {
    "fuzzy": {
      "title": {
        "value": "zhangsan",
        "fuzziness": 2	# 设置编辑距离
      }
    }
  }
}
```





### 排序

sort 可以让我们按照不同的字段进行排序，并且通过 order 指定排序的方式。desc 降序，asc 升序

```json
"sort": [
  {
    "age": {
      "order": "desc"	# 按age降序输出
    }
  },
  {
    # 其余需要加入排序的字段
  }
]
```

### 高亮

Elasticsearch 可以对查询内容中的关键字部分，进行标签和样式(高亮)的设置。

在使用 match 查询的同时，加上一个 highlight 属性:

* pre_tags:前置标签
* post_tags:后置标签
* fields:需要高亮的字段
* title:这里声明 title 字段需要高亮，后面可以为这个字段设置特有配置，也可以空



```json
"highlight": {
  "pre_tags": "<font color='red'>",
  "post_tags": "</font>",
  "fields": {
    "name": {} 
  }
}
```







### 分页返回

* from：当前页的起始索引，默认从 0 开始。 from = (pageNum - 1) * size 

* size：每页显示多少条

```json
"query": {},
"from": 0,
"size": 2
```



### 聚合查询

聚合允许使用者对 es 文档进行统计分析，类似与关系型数据库中的 group by，以及各类聚合函数。聚合函数包含在 `aggs` 中

| 关键字 |      |
| ------ | ---- |
|        |      |

#### 聚合函数

```json
{
  "aggs": {
    "max_age":{
      "max":{	# 此处填入聚合函数，如min、sum、avg等等
        "field": "age"
      }
    }
  }
}
```

#### 桶聚合

```json
{
  "aggs": {
    "age_group": {
      "terms": {	# 按其中的字段分组统计
        "field": age
      }
    }
  }
}
```



### 获取指定字段

使用`_source`可以只获取返回结果中的部分字段

```json
{
  "_source": ["name", "nickname"]	# 返回列表中的字段
}
```

在 `_source`中也可以使用 `includes`来指定想要获取的字段， `excludes`来指定不想要显示的字段

```json
{
  "_source": {
    "includes": ["nickname"],	# 会返回
    "excludes": ["name"],	# 不会反悔
  }
}
```







