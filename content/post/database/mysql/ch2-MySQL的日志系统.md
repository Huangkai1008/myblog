---
title: "MySQL的日志系统"
date: 2020-06-05T22:07:21+08:00
lastmod: 2020-06-19T18:08:24+08:00
description: ""
draft: false
tags: ["mysql"]
categories: ["mysql"]
---

# MySQL的日志系统

## 一、日志类型

> MySQL主要有两种日志类型，一种是**物理日志**（记录在某个数据页上做了什么修改)，一种是**逻辑日志**(存储了逻辑SQL修改语句)。
>
> `redo log`属于物理日志，`binlog`和`undo log`属于逻辑日志，其中物理日志的恢复速度远快于逻辑日志。
>
> `redo log`和`undo log`都属于**InnoDB**引擎层下的**事务日志（`transaction log`）**。



## 二、重做日志(redo log)

### 重做日志的基本概念
> 重做日志（redo log）是 InnoDB 引擎层的日志，用来记录事务操作引起数据的变化，记录的是数据页的物理修改，提供**前滚**操作。**MySQL 通过 redo log 保证事务的持久性**。

 重做日志由两部分组成，一是内存中的**重做日志缓冲区 (`redo log buffer`)**，它是**易失的**，另一个就是在磁盘上的**重做日志文件 (`redo log file`)**，它是**持久的**。

InnoDB 引擎对数据更新，是先将更新记录写入到重做日志，在系统空闲时或者按照设定的更新策略再将日志中的内容更新到磁盘中，这就是**预写式技术 (`Write Ahead logging, WAL`)**，这种技术可以大大减少IO操作的频率，提升数据刷新的效率。

### 重做日志的逻辑结构
redo log 的大小是固定的，为了能够持续不断的对更新记录进行写入，在redo log日志中设置了两个标志位置，`checkpoint`和`write pos`。`checkpoint`表示记录擦除的位置，`write pos`表示记录写入的位置。当`write pos`标志到了日志结尾时，会从结尾跳至日志头部循环写入，所以redo log的逻辑结构并不是线性的，可以看做一个圆周运动，逻辑结构见下图：

![redo log的逻辑结构](https://gitee.com/huanghuang927/picture-host/raw/master/20210413231000.png)

<div style="text-align: center;">图1 redo log的逻辑结构</div>

当`write_pos`追上`checkpoint`时，表示redo log日志已经写满。这时不能继续执行新的数据库更新语句，需要停下来先删除一些记录，执行`checkpoint`规则腾出可写空间。

> **checkpoint规则**：checkpoint触发后，将buffer中脏数据页和脏日志页都刷到磁盘。所谓的脏数据页就是指内存中未刷到磁盘的数据


###  重做日志的刷盘
> `redo log buffer` 写入磁盘上的`redo log file`的过程叫做**刷盘**。

在计算机操作系统中，用户空间(`user space`)下的缓冲区数据一般情况下是无法直接写入磁盘的，中间必须经过操作系统内核空间(`kernel space`)的缓冲区(`OS Buffer`)。因此，`redo log buffer`写入`redo log file`实际上是先写入`OS Buffer`，然后再通过系统调用`fsync()`将其刷到`redo log file`中，流程如下图：

![](https://gitee.com/huanghuang927/picture-host/raw/master/20211019163050.png)


当数据修改时，除了修改`buffer pool`中的数据，还会在redo log中记录这次操作。如果MySQL宕机，重启时可以读取redo log中的数据，对数据库进行恢复，从而保证了事务的持久性，**使得数据库获得`crash-safe`能力。**

#### 刷盘策略
在提交事务的时候，InnoDB会根据配置的策略来将 redo log 刷盘，这个可以通过`innodb_flush_log_at_trx_commit` 参数来配置。

```mysql
SHOW VARIABLES LIKE 'innodb_flush_log_at_trx_commit';

+--------------------------------+-------+
| Variable_name                  | Value |
+--------------------------------+-------+
| innodb_flush_log_at_trx_commit | 1     |
+--------------------------------+-------+
1 row in set (0.00 sec)
```

各参数的含义如下表：

|          参数值          |                             含义                             |
| :----------------------: | :----------------------------------------------------------: |
|     **0（延迟写）**      | 事务提交时不会将`redo log buffer`中日志写入到`OS Buffer`，而是定时写入`OS buffer`并调用`fsync()`写入到`redo log file`中。当系统崩溃，会丢失数据。 |
| **1 （实时写，实时刷）** | 事务每次提交都会将`redo log buffer`中的日志写入`OS buffer`并调用`fsync()`刷到`redo log file`中。这种方式即使系统崩溃也不会丢失任何数据，但是因为每次提交都写入磁盘，IO的性能较差。 |
| **2 （实时写，延时刷）** | 每次提交都仅写入到`OS buffer`，然后是每秒调用`fsync()`将`os buffer`中的日志写入到`redo log file`。 |

**为了保证事务的`持久性`，一般使用默认值，将 `innodb_flush_log_at_trx_commit` 设置为`1`即可。**


## 三、回滚日志 (undo log)

> 回滚日志的作用就是对数据进行回滚。当事务对数据库进行修改，InnoDB引擎不仅会记录redo log，还会生成对应的undo log日志；如果事务执行失败或调用了rollback，导致事务需要回滚，就可以利用undo log中的信息将数据回滚到修改之前的状态。
>
> 但是undo log和redo log不一样，它属于逻辑日志。它对SQL语句执行相关的信息进行记录。当发生回滚时，InnoDB引擎会根据undo log日志中的记录做与之前相反的工作。比如对于每个数据插入操作（insert），回滚时会执行数据删除操作（delete）；对于每个数据删除操作（delete），回滚时会执行数据插入操作（insert）；对于每个数据更新操作（update），回滚时会执行一个相反的数据更新操作（update），把数据改回去。undo log有两个作用，一是提供`回滚`，二是实现`MVCC`。



## 四、二进制日志 (binlog)

> 二进制日志binlog是服务层的日志，还被称为归档日志。binlog主要记录数据库的变化情况，内容包括数据库所有的更新操作。所有涉及数据变动的操作，都要记录进二进制日志中。因此有了binlog可以很方便的对数据进行复制和备份，因而也常用作主从库的同步。