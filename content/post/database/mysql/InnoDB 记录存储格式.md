---
title: "InnoDB 记录存储格式"
description: 
date: 2020-10-26T08:05:31+08:00
lastmod: 2020-10-26T08:05:31+08:00
image: "https://blog-1259169620.cos.ap-guangzhou.myqcloud.com/img/Internet_Protocol_Analysis_-_Transport_Layer.png"
math: 
license: MIT
hidden: false
comments: true
draft: false
tags: ["mysql", "innodb"]
categories: ["mysql"]
---
# InnoDB 记录存储格式

## 一、前言

根据 [MySQL的基础架构](MySQL的基础架构.md) 来看，MySQL  服务端中存储引擎负责数据的读取和写入。不同的存储引擎的形式也是不尽相同，有的存储引擎（比如 `Memory 引擎`）只把数据存储在内存、并不会持久化到磁盘，而 InnoDB 存储引擎会将数据持久化到磁盘中。

## 二、InnoDB 记录格式

### InnoDB 页简介

InnoDB 将记录存储在一个固定大小的单元中，这种单元通常被称为 **页（`page`）** ，将页作为磁盘和内存交互的基本单位。页的默认大小为16KB，可以通过以下命令查看默认页的大小（单位为字节）：

```mysql
mysql> SHOW STATUS LIKE 'innodb_page_size';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| Innodb_page_size | 16384 |
+------------------+-------+
1 row in set (0.00 sec)
```

也就是说，一般情况下，一次至少从磁盘读取16KB 的内容到内存中，一次至少把内存中的16KB 内存刷新到磁盘中。

### InnoDB 行格式

InnoDB 的行格式有4种，分别是 `COMPACT` 、`REDUNDANT`、`DYNAMIC` 、`COMPRESSED` 。可以通过如下命令查看默认行格式：

```mysql
mysql> SHOW VARIABLES LIKE 'innodb_default_row_format';
+---------------------------+---------+
| Variable_name             | Value   |
+---------------------------+---------+
| innodb_default_row_format | dynamic |
+---------------------------+---------+
1 row in set (0.01 sec)
```

可以在创建或修改表的语句中指定记录所使用的行格式。举例来说，我们创建一个示例表并插入样例数据。

```mysql
mysql> CREATE TABLE record_format (
    -> c1 VARCHAR ( 10 ),
    -> c2 VARCHAR ( 10 ) NOT NULL,
    -> c3 CHAR ( 10 ),
    -> c4 VARCHAR ( 10 )
    -> ) CHARSET=ascii ROW_FORMAT=COMPACT;
Query OK, 0 rows affected (0.02 sec)
```

![创建测试表](https://blog-1259169620.cos.ap-guangzhou.myqcloud.com/img/%E6%88%AA%E5%B1%8F2022-07-09%2009.45.32.png)

#### COMPACT 行格式

COMPACT 行格式的结构大概如下图所示：

![COMPACT 行格式](https://blog-1259169620.cos.ap-guangzhou.myqcloud.com/img/compact%E8%A1%8C%E6%A0%BC%E5%BC%8F.png)

一条记录可以分为两部分：记录的额外信息和记录的真实数据。

##### 记录的额外信息

###### 变长字段长度列表

MySQL 中有些类型的字段长度是不固定的，比如 VARCHAR(M) 类型、TEXT 等，这就导致每条记录中该字段的「实际」长度可能是不一样的。

为此，MySQL 在存储这些变长类型的数据时，实际上分成了两部分存储，分别是：

1. 真正的数据内容
2. 该数据占用的字节数

在 COMPACT 行格式中，所有变长字段的真实数据占用的字节数都存储在记录的 **变长字段长度列表** 中。它是以列的 **逆序** 存储表中变长字段的实际长度的。

因为我们使用的是 ascii 字符集，每个字符只需要一个字节来编码，所以对于表中的第一条记录有：

![变长字段长度列表](https://blog-1259169620.cos.ap-guangzhou.myqcloud.com/img/%E6%88%AA%E5%B1%8F2022-07-09%2009.59.25.png)

变长字段 c1、c2、c4 所占用的字节数分别为 4、2、1，以逆序存放在变长字段长度列表表示为 010304。

例如某个字符集最多需要  $W$ 字节来表示一个字符，变长类型 VARCHAR(M) 表示最多可以存储 $M$ 个字符，所以这种类型能表示的字符串最多占用的字节数就是 $M \times W$ 。

再假设该变长字段实际存储的字符串占用的字节数是 $L$ 。

如果 $M \times W \leq 255$，那么使用1字节来表示真实数据占用的字节数；

如果 $M \times W > 255$：

* $L \leq 127$ : 使用1字节来表示真实数据占用的字节数；
* $L > 127$：使用2字节来表示真实数据占用的字节数。

值得注意的是，**变长字段列表中只存储非 NULL 的列的内容长度**。

###### NULL 值列表

MySQL 中有些列是允许为 NULL 的，如果这些列很多、每个 NULL 值都在表中存储的话会很占用空间。Compact 把这些 NULL 统一管理了起来，放到了 **NULL 值列表**。

它的处理过程如下：

1. 统计表中允许为 NULL 的列：
若列都不允许为 NULL，则 NULL 值列表就不存在了；否则，以一个二进制位来表示一个允许为空的列，仍是**逆序**排列，其中 1 表示 NULL，0 表示非 NULL。
2. 若 NULL 值列表不足整数字节，在高位补 0。

以第二条数据为例 c1、c3、c4 允许为 NULL，并且c3、c4的值是NULL。

![第2条数据](https://blog-1259169620.cos.ap-guangzhou.myqcloud.com/img/%E6%88%AA%E5%B1%8F2022-07-09%2011.05.54.png)

![NULL值列表](https://blog-1259169620.cos.ap-guangzhou.myqcloud.com/img/%E6%88%AA%E5%B1%8F2022-07-09%2011.04.43.png)

###### 记录头信息

记录头信息由固定的5字节组成，用于描述记录的一些属性。

详细信息如下表:

|     名称     | 大小（位） |                     描述                      |
|:------------:|:----------:|:---------------------------------------------:|
|   预留位1    |     1      |                    未使用                     |
|   预留位2    |     1      |                    未使用                     |
| deleted_flag |     1      |             标记该记录是否被删除              |
| min_rec_flag |     1      | B+ 树每层非叶子节点中的最小记录都会添加该标记 |
|   n_owned    |     4      |             当前记录拥有的记录数              |
|   heap_no    |     13     |         当前记录在页面堆中的相对位置          |
| record_type  |     3      |                当前记录的类型                 |
| next_record  |     16     |             下一条记录的相对位置              |

##### 记录的真实数据

除了用户自定义的列外，InnoDB 会为每个记录添加 **隐藏列**，具体如下表所示：

|     列名     | 是否必需 | 占用空间 |          描述          |
|:------------:|:--------:|:--------:|:----------------------:|
|    row_id    |    否    |  6 字节  | 行ID，标识唯一一条数据 |
|    trx_id    |    是    |  6字节   |         事务ID         |
| roll_pointer |    是    |  7字节   | 回滚指针                       |


说起 row_id，有必要提一下 InnoDB 的主键生成策略，它遵循如下顺序：
1.  优先使用用户定义的主键
2.  若未定义主键，则选取唯一键作为主键
3.  若无唯一键，添加 row_id 作为主键

即，当我们新建一个表时，若没有指定主键（Primary Key），InnoDB 会选择一个唯一键（Unique Key）作为主键，如果表中唯一键也没定义，则就要添加一个隐藏列 row_id 来充当主键了。

#### REDUNDANT、DYNAMIC、COMPRESSED 行格式

**REDUNDANT** 是一种比较原始的行格式，现在已经用的很少了，它是非紧凑的。**COMPACT 、DYNAMIC、COMPRESSED** 都是较新的紧凑的行格式。

像现在用的较多的 5.7以及8.0版本的MySQL ，默认行格式都是 **DYNAMIC** 。

DYNAMIC 和 COMPRESSED 行格式类似于 COMPACT ，主要区别在于[行溢出](InnoDB%20记录存储格式.md#行溢出)的处理。

另外，COMPRESSED 行格式还会采用压缩算法对页面进行压缩。

#### 行溢出

我们知道，页的大小是有限的，假设页的大小为16KB， 那么当单行的数据超出了页的最大范围，那么就会产生行的溢出。对于这种情况，各种行格式有着不同的处理方法。

对于`Compact`和`Redundant`行格式来说，如果某一列中的数据非常多的话，在本记录的真实数据处只会存储该列的前 **768** 个字节的数据和一个指向其他页的地址，然后把剩下的数据存放到其他页中。

而`Dynamic`和`Compressed`行格式会把所有的字节都存储到其他页面中，只在记录的真实数据处存储其他页面的地址。·

## 参考资料

* 小孩子 4919. (2020). _MySQL 是怎样运行的_. 人民邮电出版社.
* [InnoDB Page Structure](https://dev.mysql.com/doc/internals/en/innodb-page-structure.html)
