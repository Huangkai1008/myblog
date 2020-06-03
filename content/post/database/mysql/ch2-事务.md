---
title: "事务"
date: 2020-06-02T22:25:20+08:00
lastmod: 2020-06-03T22:01:24+08:00
description: ""
draft: false
tags: ["mysql", "transaction"]
categories: ["mysql"]
---

## 概念

> 事务就是一组原子性的SQL查询，或者说一个独立的工作单元。如果数据库引擎能够成功地对数据库应用该组查询的全部语句，那么就执行该组查询。如果其中有任何一条语句因为崩溃或其他原因无法执行，那么所有的语句都不会执行。也就是说，事务内的语句，要么全部执行成功，要么全部执行失败。**在 MySQL 中，事务支持是在引擎层实现的。**

## ACID特性

- **原子性（Atomicity）**：事务作为一个整体被执行，包含在其中的对数据库的操作要么全部被执行，要么都不执行

- **一致性（Consistency）**：事务应确保数据库的状态从一个一致状态转变为另一个一致状态。一致状态的含义是数据库中的数据应满足完整性约束。

- **隔离性（Isolation）**：**通常来说**，一个事务所做的修改在最终提交以前，对其他事务是不可见的。

- **持久性（Durability）**：已被提交的事务对数据库的修改应该永久保存在数据库中。

  

> 一个实现了ACID的数据库，相比没有实现ACID的数据库，通常会需要更强的CPU处理能力、更大的内存和更多的磁盘空间。

## 隔离级别（Isolation level）

* **READ UNCOMMITTED（读未提交）**

  ​		事务中的修改，即使没有提交，对其他事务也都是可见的。事务可以读取未提交的数据，这也被称为`脏读（Dirty Read）`。

* **READ COMMITTED（读提交）**

  ​		Oracle和SQL Server的默认隔离级别。一个事务可以读取另一个已提交的事务。换句话说，一个事务从开始直到提交之前，所做的任何修改对其他事务都是不可见的。这个级别有时候也叫做不可重复读（nonrepeatable read），因为两次执行同样的查询，可能会得到不一样的结果。MySQL的InnoDB引擎在提交读级别通过MVCC解决了不可重复读的问题

* **REPEATABLE READ（可重复读）**

  ​	   **MySQL的默认隔离级别**。一个事务执行过程中看到的数据，总是跟这个事务在启动时看到的数据是一致的。当然在可重复读隔离级别下，未提交变更对其他事务也是不可见的。**MySQL的InnoDB引擎在提交读级别通过MVCC解决了不可重复读的问题，在可重复读级别通过间隙锁解决了幻读问题**。
  
* **SERIALIZABLE（可串行化）**

  ​		SERIALIZABLE是最高的隔离级别。它通过强制事务串行执行，避免了前面说的幻读的问题。简单来说，SERIALIZABLE会在读取的每一行数据上都加锁，所以可能导致大量的超时和锁争用的问题。实际应用中也很少用到这个隔离级别，只有在非常需要确保数据的一致性而且可以接受没有并发的情况下，才考虑采用该级别。

<div style="text-align: center;">ANSI SQL隔离级别</div>

|     隔离级别     | 脏读可能性 | 不可重复读可能性 | 幻读可能性 | 加锁读 |
| :--------------: | :--------: | :--------------: | :--------: | :----: |
| READ UNCOMMITTED |     √      |        √         |     √      |   ×    |
|  READ COMMITTED  |     ×      |        √         |     √      |   ×    |
| REPEATABLE READ  |     ×      |        ×         |     √      |   ×    |
|   SERIALIZABLE   |     ×      |        ×         |     ×      |   √    |

>  查看MySQL的隔离级别

```mysql
SHOW VARIABLES LIKE 'transaction_isolation';

+-----------------------+-----------------+
| Variable_name         | Value           |
+-----------------------+-----------------+
| transaction_isolation | REPEATABLE-READ |
+-----------------------+-----------------+
1 row in set, 1 warning (0.00 sec)
```

> 设置当前会话的隔离级别

```mysql
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;	# 设置当前会话为RC级别，下个事务生效
```

## 事务类型

### 隐式事务

DML操作的语句都会隐式的开启事务，并且在语句执行后没有错误的话隐式提交。可以通过将MySQL的`autocommit`这个变量（默认为1）设置为0将事务的`隐式提交`关闭，但需要注意，DML语句的隐式事务仍会启动，只是区别在于需要手动COMMIT显式提交这个事务，也就是将隐式事务转化为长事务了。

```mysql
SHOW VARIABLES LIKE 'autocommit'; 	# 查看隐式事务提交方式

+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit    | ON    |
+---------------+-------+
1 row in set, 1 warning (0.00 sec)
```

### 显式事务

```mysql
# 1.显式开启一个事务
START TRANSACTION;
BEGIN;
# 2.提交事务
COMMIT;
# 3.回滚事务
ROLLBACK;
# 4.在事务中创建保存点，可以在同一事务中创建多个，以便通过ROLLBACK更灵活的回滚
SAVEPOINT;
```

显式开启一个事务时，如果还有未提交的事务会自动提交，并且`autocommit`会被禁用直到该事务结束。对于显式事务，存在`completion_type`这样一个变量控制显式事务的行为。有下列三种情况：

- 值为0时即为默认，执行COMMIT后提交该显式事务并结束该事务。
- 值为1时，执行COMMIT后除了有值为0时的默认行为外，随后会自动开始一个相同隔离级别的事务。术语为`COMMIT AND CHAIN`
- 值为2时，执行COMMIT后除了有值为0时的默认行为外，随后会断开与服务器的连接。术语为`COMMIT AND RELEASE`




