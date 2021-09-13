

## binlog（归档日志）

> binlog是位于Server层的日志，记录的是语句的原始逻辑。
>
> 可用于MySQL的数据备份、主从一致保证等操作，也可以通过解析binlog实现mysql到其他数据源（如ElasticSearch）的数据复制与同步

### 与redo log的区别

1. redo log是InnoDB引擎特有的；binlog是MySQL的Server层实现的，所有引擎都可以使用。
2. redo log是物理日志，记录的是“在某个数据页上做了什么修改”；binlog是逻辑日志，记录的是这个语句的原始逻辑，比如“给ID=2这一行的c字段加1 ”。
3. redo log是循环写的，空间固定会用完；binlog是可以追加写入的。“追加写”是指binlog文件写到一定大小后会切换到下一个，并不会覆盖以前的日志。

### binlog三种格式（binlog_format）

* statement：可以理解为存储的就是我们运行的SQL语句
* row：记录了真实的操作行的id

* mixed：混合了以上两种情况。安全的时候用statement，否则使用row

statement的缺点：比如当使用`delete from t where XXXX limit 1;`时，由于可能走的索引不一样，会造成删除的数据不一致，会出现主备数据不一致的情况（这种情况是unsafe的）。因此**至少应该把binlog的格式设置为mixed**

row的缺点：比较占用空间。比如要删除1万条数据，statement中只需要记录一句话，而row中则需要记录1万条数据的id

但是通常应该使用raw，原因是在恢复数据时有显著优势（由于直接记录了对应行的信息）

### MySQL怎么知道binlog是完整的

一个事务的binlog有完整格式

- statement格式的binlog，最后会有COMMIT；
- row格式的binlog，最后会有一个XID event。



## redo log（重做日志）

> InnoDB引擎特有的日志，可用于宕机后的数据恢复，保证了数据的持久性

当有一条记录需要更新时，InnoDB会先将记录写入redo log中，并更新内存，之后在适当的时候将其更新至磁盘

redo log的大小是固定的，进行循环写入

Write-Ahead Logging，它的关键点就是先写日志，再写磁盘

### 两阶段提交

将redo log的写入拆成了两个步骤：prepare和commit，这就是"两阶段提交"。

InnoDB就可以保证即使数据库发生异常重启，之前提交的记录都不会丢失，这个能力称为**crash-safe**

redo log和binlog都可以用于表示事务的提交状态，而两阶段提交就是让这两个状态保持逻辑上的一致

### redo log与binlog的关联

有一个共同字段（XID）

崩溃恢复的时候，会按顺序扫描redo log

- 如果碰到既有prepare、又有commit的redo log，就直接提交；
- 如果碰到只有parepare、而没有commit的redo log，就拿着XID去binlog找对应的事务。



## undo log（回滚日志）

除了记录redo log外，当进行数据修改时还会记录undo log，undo log用于数据的撤回操作，它记录了修改的反向操作，比如，插入对应删除，修改对应修改为原来的数据，通过undo log可以实现事务回滚，并且可以根据undo log回溯到某个特定的版本的数据，实现MVCC。

Undo log分为Insert和Update两种，delete可以看做是一种特殊的update，即在记录上修改删除标记。

* update undo log：记录了数据之前的数据信息，通过这些信息可以还原到之前版本的状态。不仅在事务回滚时需要，在快照读时也需要

* Insert undo log：在事务提交后即可删除，因为其他事务不需要这个undo log。因此只在事务回滚时需要





参考：https://mp.weixin.qq.com/s/KUv-Mx-FRrfQpx09Xv5Itg



