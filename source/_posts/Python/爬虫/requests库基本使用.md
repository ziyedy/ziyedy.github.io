---
title: requests库基本使用
date: 2020-08-13 16:09:39
tags:
	- Python
categories:
	- Python
	- 爬虫
fileName: python-requests-basic
---

**requests库是python实现的最简单易用的HTTP库。**

### 主要方法



```
requests.request(method, url, **kwargs)
```

method : 请求方式，对应get/put/post等7种
url : 拟获取页面的url链接
**kwargs: 控制访问的参数，共13个



### get方法

基本使用如下：

```
r = requests.get(url)
```

其中r是返回的一个包含服务器资源的**Response对象**

get方法是构造了一个向服务器请求资源的**Request对象**



### Response对象的属性



#### 判断异常

r.raise_for_status()在方法内部判断r.status_code是否等于200，不需要增加额外的if语句，该语句**便于利用try‐except进行异常处理**

```
r.raise_for_status()
```



### 爬取网页通用代码框架

```
def getHTMLText(url):
    import requests
    try:
        r = requests.get(url)
        r.raise_for_status()
        r.encoding = r.apparent_encoding
        return r.text
    except:
        print("产生异常")
        return None
```













