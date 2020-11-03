---
title: "InnoDB 索引"
description: 
date: 2020-11-03T08:40:31+08:00
lastmod: 2020-11-03T-08:40:31+08:00
image: "https://blog-1259169620.cos.ap-guangzhou.myqcloud.com/img/%E6%88%AA%E5%B1%8F2022-07-10%2010.15.54.png"
math: 
license: MIT
hidden: false
comments: true
draft: false
tags: ["mysql", "innodb"]
categories: ["mysql"]
---

# InnoDB 的索引

## 一、前言

## 什么是索引

**索引（`index`）** 的出现是为了提高数据查询的效率，索引的实现一般会使用特定的数据结构，我们可以使用不同的数据结构实现不同的索引模型。

## 索引的常见模型

索引的常见模型有 **哈希表（`hash-table`）** 、**有序数组（`array`）** 和 **搜索树（`search tree`）**。

其中**哈希表适用于只有等值查询的场景**，例如 Memcached 及其他一些 NoSQL 引擎。

**有序数组在等值查询和范围查询场景中的性能都很优秀**，但是对于需要更新数据的场景就显得成本太高，**所以有序数组索引只适用于静态存储引擎** 。

关于搜索树，我们最熟悉的应该是 **二分搜索树（`binary-search tree`）**。如果需要维持 `O(log(N))` 的查询复杂度和更新复杂度，则需要维护这个二分搜索树为平衡二叉树。为了让一个查询尽量少地读磁盘，就必须让查询过程访问尽量少的数据块。那么，我们就不应该使用二叉树，而是要使用“N 叉”树。这里，“N 叉”树中的 `N` 取决于数据块的大小。

## 二、InnoDB 的索引模型

