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



## 二、重做日志(redo log)

### 基本概念
> 重做日志（redo log）是 InnoDB 引擎层的日志，用来记录事务操作引起数据的变化，记录的是数据页的物理修改，提供**前滚**操作。**MySQL 通过 redo log 保证事务的持久性**。

 重做日志由两部分组成，一是内存中的**重做日志缓冲区 (`redo log buffer`)**，它是**易失的**，另一个就是在磁盘上的**重做日志文件 (`redo log file`)**，它是**持久的**。

InnoDB 引擎对数据更新，是先将更新记录写入到重做日志，在系统空闲时或者按照设定的更新策略再将日志中的内容更新到磁盘中，这就是**预写式技术 (`Write Ahead logging, WAL`)**，这种技术可以大大减少IO操作的频率，提升数据刷新的效率。

### 逻辑结构
redo log 的大小是固定的，为了能够持续不断的对更新记录进行写入，在redo log日志中设置了两个标志位置，`checkpoint`和`write pos`。`checkpoint`表示记录擦除的位置，`write pos`表示记录写入的位置。当`write pos`标志到了日志结尾时，会从结尾跳至日志头部循环写入，所以redo log的逻辑结构并不是线性的，可以看做一个圆周运动，逻辑结构见下图：

![redo log的逻辑结构](https://gitee.com/huanghuang927/picture-host/raw/master/20210413231000.png)

<div style="text-align: center;">图1 redo log的逻辑结构</div>

当`write_pos`追上`checkpoint`时，表示redo log日志已经写满。这时不能继续执行新的数据库更新语句，需要停下来先删除一些记录，执行`checkpoint`规则腾出可写空间。

> **checkpoint规则**：checkpoint触发后，将buffer中脏数据页和脏日志页都刷到磁盘。所谓的脏数据页就是指内存中未刷到磁盘的数据


###  刷盘
> `redo log buffer` 数据页写入磁盘中的`redo log file`的过程叫做**刷盘**。

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

## 三、二进制日志 (binlog)

### 基本概念

> `binlog`（**二进制日志**、**归档日志**），用于记录数据库执行的写入性操作信息，以二进制的形式保存在磁盘中。

### 使用场景

1. **主从复制**：从库利用主库上的 binlog 进行重播，实现主从同步
2. **数据恢复**：用于数据库的基于时间点、位点等的还原操作（`mysqlbinlog`）

### 日志格式
`binlog`日志有三种格式，分别为`STATMENT`、`ROW`和`MIXED`。

> 在 MySQL 5.7.7之前，默认的格式是`STATEMENT`，MySQL 5.7.7之后，默认值是`ROW`。日志格式通过`binlog-format`指定。

#### **`STATEMENT`**
`STATMENT` **基于SQL语句的复制(`statement-based replication, SBR`)**，会记录每一条修改数据的SQL语句和执行语句的上下文信息

##### **优点** 
`STATMENT` 模式不需要记录每一行的变化，减少了`binlog`的日志量，节省了I/O和存储资源,，从而提高了性能
##### **缺点**
`STATMENT` 模式在某些情况下会导致主从数据不一致，比如执行`sysdate()`、`sleep()`等

#### **`ROW`**
`ROW` **基于行的复制(`row-based replication, RBR`)**，会记录每一行数据被修改的形式

##### **优点**
`ROW` 模式下的日志内容会非常清楚的记录下每一行数据的修改细节，非常容易理解，而且不会出现某些特定情况下的存储过程和 function，以及 trigger 的调用和触发无法被正确复制问题

##### **缺点**
`ROW` 模式下会产生大量的日志

#### **MIXED**
`MIXED` **基于`STATMENT`和`ROW`两种模式的混合复制(`mixed-based replication, MBR`)**，一般的复制使用`STATEMENT`模式保存，对于`STATEMENT`模式无法复制的操作使用`ROW`模式保存

### 刷盘策略
**MySQL 只有在事务提交的时候才会记录 binlog 日志**，此时日志还在内存中，MySQL 通过`sync_binlog`参数控制 biglog 的刷盘时机，取值范围是`0-N`：

* **0**： 不做强制要求，由系统自行判断何时写入磁盘

* **1**：每次事务提交时 binlog 都会写入磁盘

* **N**：每N个事务 binlog 会写入磁盘

  

`sync_binlog`最安全的是设置是**1**，这也是 MySQL 5.7.7之后版本的默认值，但是也可以设置一个大一些的值可以提升数据库性能，因此实际情况下也可以将值适当调大，牺牲一定的一致性来获取更好的性能。

### 与重做日志的区别

|              | `redo log`             | `binlog`                                         |
| :----------: | ---------------------- | ------------------------------------------------ |
| **实现方式** | InnoDB引擎特有的       | MySQL的Server层实现的，所有引擎都可以使用        |
| **日志类型** | 物理日志               | 逻辑日志                                         |
| **写入方式** | 循环写，空间固定会用完 | 追加写入，binlog文件写到一定大小后会切换到下一个 |
| **适用场景** | 崩溃恢复(`crash-safe`) | 主从复制和数据恢复                               |

### 两阶段提交（`Two-phase Commit，2PC`）

 MySQL 事务提交的时候，需要同时完成 `redo log` 和 `binlog` 的提交，为了让两份日志之间的逻辑一致，需要用到**两阶段提交**，这个场景下的两阶段提交发生在 MySQL 内部，和分布式系统的两阶段提交是两个概念。

![](https://gitee.com/huanghuang927/picture-host/raw/master/20211020170253.svg)

## 四、回滚日志 (undo log)

> 当事务对数据库进行修改，InnoDB引擎不仅会记录redo log，还会生成对应的undo log日志；如果事务执行失败或调用了rollback，导致事务需要回滚，就可以利用undo log中的信息将数据回滚到修改之前的状态。**MySQL 通过 undo log 保证事务的原子性**。undo log有两个作用，一是提供**回滚**，二是实现 **`MVCC`**

回滚日志并不能将数据库物理地恢复到执行语句或者事务之前的样子；它是逻辑日志，当回滚日志被使用时，它只会按照日志逻辑地将数据库中的修改撤销掉，可以理解为，我们在事务中使用的每一条 `INSERT` 都对应了一条 `DELETE`，每一条 `UPDATE` 也都对应一条相反的 `UPDATE` 语句。

### 事务日志

在数据库系统中，事务的**原子性**和**持久性**是由**事务日志（`transaction log`）**保证的，而`redo log`和`undo log`都属于**InnoDB**引擎层下的**事务日志（`transaction log`）**。这两种事务日志可以保证：

1. 发生错误或者需要回滚的事务能够成功回滚（原子性）
2. 在事务提交后，数据没来得及写会磁盘就宕机时，在下次重新启动后能够成功恢复数据（持久性）

在数据库中，这两种日志经常都是一起工作的，可以将它们整体看做一条事务日志，其中包含了事务的 ID、修改的行元素以及修改前后的值。



一条事务日志同时包含了修改前后的值，能够非常简单的进行回滚和重做两种操作。
