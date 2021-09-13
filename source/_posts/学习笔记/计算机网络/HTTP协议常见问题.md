---
title: HTTP协议常见问题
date: 2021-04-11 12:50:14
tags:
	- 网络协议
categories:
	- 学习笔记
	- 计算机网络
fileName: HTTP
---



- HTTP 的 Keep-Alive，是由**应用层（用户态）** 实现的，称为 HTTP 长连接；
- TCP 的 Keepalive，是由 **TCP 层（内核态）** 实现的，称为 TCP 保活机制；





## 问题汇总


1.5 TCP第三次握手可以传输数据吗

### 2. TCP和UDP的区别

### 3. TCP的可靠传输

3.1 TCP如何确保可靠性传输
3.2 TCP的拥塞控制
3.3 TCP传输通信时,[客户端](https://www.nowcoder.com/jump/super-jump/word?word=客户端)突然断开连接,服务端如何判断
3.4 TCP的端口时为了区分什么

### 4. 常见的HTTP状态码

### 5. HTTP报文

5.1 HTTP请求报文和响应报文的组成
5.2 HTTP请求报文包含哪些方法, GET和POST的区别

### 6. HTTP和HTTPS的区别

### 7. HTTP1.0和1.1和2.0的区别

### HTTP 1.0和HTTP 1.1的主要区别是什么

1. 连接：**HTTP/1.0中，默认使用的是短连接**，也就是说每次请求都要重新建立一次连接；**HTTP 1.1起，默认使用长连接**,默认开启`Connection： keep-alive`
2. 错误状态响应码：**HTTP1.1中新增了24个错误状态响应码**
3. 缓存处理：在HTTP1.0中主要使用header里的If-Modified-Since,Expires来做为缓存判断的标准，HTTP1.1则引入了更多的缓存控制策略例
4. 带宽优化及网络连接的使用：HTTP1.1则在请求头引入了range头域，它允许只请求资源的某个部分，即返回码是206（Partial Content），这样就方便了开发者自由的选择以便于充分利用带宽和连接

HTTP/2.0：

1、采用二进制格式而非文本格式，将所有传输的信息分割为更小的消息和帧

2、首部压缩



### 8. HTTPS密钥交换过程

https://www.jianshu.com/p/b1538c758c4a

### 9. HTTP的缓存机制



### 10. 输入URL跳转网页的过程



## cookie与session

### 区别



### 禁用cookie



### cookie的跨域共享

正常的Cookie只能在一个应用中访问，使用`setDomain()` 等方法能够使得cookie实现跨域共享

即 a. jd.com 和 b.jd.com 都能够共享 jd.com 的 Cookie



### 13. 页面加载不出来的原因



a.jd.com能否共享的Cookie？呢？

跨域共享

[cookie的跨域共享和setDomain()方法_weixin_42053164的博客-CSDN博客](https://blog.csdn.net/weixin_42053164/article/details/102921758)



### WebSocket

推送功能：

服务器可直接发送数据，而不必等待客户端的请求

减少通信量：

首部信息小，减少开销