---
title: Kafka高可用架构设计
date: 2021-07-04 11:14:15
tags:
categories:
fileName:
---
# 基础架构及术语

## 高可用

高可用性（High Availability）：指系统无间断地执行其功能的能力，代表系统的可用性程度

Kafka从0.8版本开始提供了高可用机制，可保障一个或多个Broker宕机后，其他Broker及所有Partition都能继续提供服务（失败恢复机制），且存储的消息不丢失（多机备份、消息应答）

## 架构设计思想

基本思想：生产者发布一个消息到Kafka的一个Topic，存放于Broker中，消费者订阅该Broker，并且从Broker中消费消息

* 消费组（Consumer Group）：Kafka按消费组来消费消息，一个消费组下面的所有机器可以组成一个Consumer Group，每条消息只能被该Consumer Group一个Consumer消费，不同Consumer Group可以消费同一条消息
* 消息状态：Kafka中消息是否被消费的状态是保存在Consumer中的，Broker不会关心消息是否被消费或被谁消费（Consumer会记录一个offset值，用于指向下一条要被消费的消息的位置），因此offset值设置错误可能会导致消息丢失或重复消费
* 消息持久化：Kafka会把消息持久化到本地文件系统
* 批量发送：Kafka支持以消息集合为单位批量进行发送
* Push-and-Pull：Kafka中Producer向Broker中Push消息，而Consumer从Broker中Pull消息
* 分区机制（Partition）：Broker支持消息分区，Producer可以决定将消息发送到哪个分区中。一个Topic中的分区个数是可配置的。分区机制是Kafka高吞吐量的重要保证

## 架构术语

* Broker：一个Kafka集群中的一台服务器就是一个Broker，Broker可以水平无限扩展，同一个Topic中的消息可以分布在多个Broker中
* Consumer：Consumer通过向 Broker 发出一个“Fetch”请求来获取它想要消费的消息，Consumer 的每个请求都在消息文件中指定了对应的 offset，并接收从该位置开始的一大块数据。因此，Consumer 对于该位置的控制就显得极为重要，并且可以在需要的时候通过回退到某个位置再次消费对应的数据
* Producer：将客户端生产的Message发送到Broker中的Partition Leader节点，Producer可以通过配置保证写入的消息不会丢失， Producer同时支持消息异步发送、批量发送

## 业界应用

异步通信、日志同步、实时计算



# 消息备份机制

Kafka的高可靠性的保障来源于其健壮的副本（replication）策略。

Kafka将为每个partition提供多个replication，同时将replication分布到整个集群的其它Broker中，具体的replication数量可以通过参数设置，这里的replication会选举一个Leader节点，其它节点为Follower节点，消息发送全部发送到Leader然后再通过同步算法同步到Follower节点中，当其中有replication不能工作会重新进行选举，即使部分Broker宕机仍然能保证整个集群高可用，消息不丢失

## ISR

ISR（In-Sync Replicas）指一个Partition中与Leader保持同步的Replica列表（存储的是副本所在的Broker的BrokeId）

保持同步指在*replica.lag.time.max.ms*时间内与Leader保持有效连接（If a follower hasn't sent any fetch requests or hasn't consumed up to the leaders log end offset for at least this time, the leader will remove the follower from isr, default value =10000 ）

各Partition的Leader负责维护ISR列表并将ISR的变更同步至ZooKeeper，被移出ISR的Follower会继续向Leader发FetchRequest请求，试图再次跟上Leader重新进入ISR。注意，*默认配置下，只有ISR中的成员才有可能被选为Leader*

## ACKS

producer向leader发送数据时，可以通过request.required.acks参数来设置数据可靠性的级别

* 0：这意味着producer无需等待来自broker的确认而继续发送下一批消息，这种情况下数据传输效率最高，但是数据可靠性确是最低的（无保证）

*  1：这意味着producer在replication中的leader已成功收到的数据并得到确认后发送下一条message，如果leader宕机了，则会丢失数据（只保证leader收到消息）
* -1：producer需要等待replication中的所有follower都确认接收到数据后才算一次发送完成，可靠性最高，但是这样也不能保证数据不丢失，比如当replication中只有leader时，这样就变成了acks=1的情况，因此可以通过设置`min.insync.replicas >= 2`保证数据一定不丢失（保证了leader与所有follower都收到消息）

## LEO & HW

* LEO（log end offset）：日志末端偏移，指向了日志中下一个消息的写入位置
* HW（high watermark）：已同步消息标识。该标识之前的消息都是已备份过的，消费者只可消费各分区leader高水位线以下的消息，对于任何一个副本对象而言其HW值不会大于LEO值，**Leader的HW值由ISR中的所有备份的LEO最小值决定**（Follower在发送FetchRequest时会在PartitionFetchInfo中会携带Follower的LEO）。



# 故障恢复

## Broker故障恢复

### 场景分析

#### 1、Broker与其他Broker断开连接

> 假设Broker0与Zookeeper断开连接，其上有Partition0的leader 与 Partition1的follower

* Broker中的leader：当超过*replica.lag.time.max.ms*没接收到其他follower的FetchRequest请求时，会将ISR收缩到仅剩该Broker本身，同时将ISR的变动同步到ZooKeeper
* Broker中的folower：超过*replica.lag.time.max.ms*后，其leader会将该Broker中的副本从ISR中移除。若后续恢复连接，则还会再将其重新加入对应的ISR

#### 2、Broker与ZooKeeper断开连接

> 假设Broker0与Zookeeper断开连接，其上有Partition0的leader 与 Partition1的follower

Broker与与ZooKeeper断开连接后，ZooKeeper会自动删除该Broker对应节点，并且认为该Broker已经宕机

对于Broker0中的leader：

​	ZooKeeper删除节点后，该节点上注册的Watcher会通知Controller，Controller会发现Broker0为Partition0的Leader，于是从当前存活的ISR中选择了Broker1作为Partition0的新Leader。Controller通过LeaderAndIsrRequest将Leader变更通知到所有Broker，于是其余Broker改向Broker1发送Partition0数据的FetchRequest请求。

​	生产者：发现Partition0的Leader发生变更后，会改向新Leader-Broker1发送Partition0数据。另一边，Broker0收不到ZooKeeper通知，依然认为自己是Partition0的Leader；由于Broker1、Broker2不再向Broker0发送FetchRequest请求，缺失了ISR应答的Broker0停止写入acks=all的消息，但可以继续写入acks=1的消息。在*replica.lag.time.max.ms*时间后，Broker0尝试向ZooKeeper发送ISR变更请求但失败了，于是不再接收生产者的消息。

​	当Broker0与ZooKeeper恢复连接后，发现自己不再是Partition0的Leader，于是将本地日志截断（为了保证和Leader数据一致性），并开始向Broker2发送FetchRequest请求。在Broker0与ZooKeeper失联期间写入Broker0的所有消息由于未在新Leader中备份，这些消息都丢失了。

对于Broker0中的副本：

​	副本只是作为Partition1的Follower节点，而Broker0与Broker1依然保持连接，因此Broker0依然会向Broker1发送FetchRequest。只要Broker0能继续保持同步，Broker1也不会向ZooKeeper变更ISR。

### 故障恢复过程（场景2）

Broker发生故障后，由Controller负责选举受影响Partition的新Leader并通知到相关Broker

TODO：理解Zookeeper之后补充

## Controller故障恢复

### 场景分析

#### 1、Controller与ZooKeeper断开连接

此时ZooKeeper会将Controller临时节点删除，并按照下节的故障恢复过程重新竞选出新Controller。而原本的Controller由于无法连上ZooKeeper，它什么也执行不了；当它与ZooKeeper恢复连接后发现自己不再是Controller，会在Kafka集群中充当一个普通的Broker

#### 2、Controller与某个Broker断开连接

因为Controller无法起到通知的作用，可能会发生上一节 “Broker与ZooKeeper断开连接”的问题

### 故障恢复过程

Kafka中所有Broker都在ZooKeeper的Controller节点上注册一个Watcher。Controller发生故障时对应的Controller临时节点会自动删除，此时注册在其上的Watcher会被触发，所有活着的Broker都会去竞选成为新的Controller（即创建新的Controller节点，由ZooKeeper保证只会有一个创建成功）。竞选成功者即为新的Controller，会在ZooKeeper的下述节点上注册Watcher，以监控各Broker运行状态、负责Leader宕机的失败恢复，并对管理脚本做出响应。

TODO：理解Zookeeper之后补充



## Kafka参考

https://jack-vanlightly.com/blog/2018/9/2/rabbitmq-vs-kafka-part-6-fault-tolerance-and-high-availability-with-kafka

