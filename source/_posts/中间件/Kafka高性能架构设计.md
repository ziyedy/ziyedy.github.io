---
title: Kafka高性能架构设计
date: 2021-07-04 11:14:15
tags:
categories:
fileName:
---

## 高性能分析

* 消息传递会涉及大量的网络传输，如果Producer每生产一个消息就发送到Broker会造成大量的网络消耗，严重影响到Kafka的性能。因此，Kafka使用了**批量发送**的方式
* Broker在持久化消息、读取消息的时候，如果采用传统的IO读写方式也会降低性能。因此，Kafka采用了**顺序写+零拷贝**的方式



## 消息批量发送

### 消息路由

Kafka中Topic有多个Partition，消息分配到某个Partition的策略称为路由策略，主要有三种：

* Round Robin：Producer将消息均衡地分配到各Partition本地队列上，是最常用的分区策略
* 散列：Kafka对消息的key进行散列，根据散列值将消息路由到特定的Partition上，**键相同的消息总是被路由到相同的Partition上**

* 自定义分区策略：Kafka支持自定义分区策略，可以将某一系列的消息映射到相同的Partition

## 消息持久化

### 顺序写



### 零拷贝

 Kafka中存在大量的网络数据持久化到磁盘（Producer到Broker）和磁盘文件通过网络发送（Broker到Consumer）的过程，Kafka采用零拷贝这一通用技术解决该问题

> 零拷贝技术可以减少数据拷贝和共享总线操作的次数，消除传输数据在存储器之间不必要的中间拷贝次数，减少用户应用程序地址空间和操作系统内核地址空间之间因为上下文切换而带来的开销，从而有效地提高数据传输效率







