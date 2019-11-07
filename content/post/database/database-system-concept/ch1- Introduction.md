---
title: "数据库系统概论-介绍"
date: 2018-09-09T13:56:20+08:00
lastmod: 2018-09-09T13:56:20+08:00
description: ""
draft: false
tags: ["database", "database-system-concept"]
categories: ["database", "database-system-concept"]
---

## 数据库系统的目的(`Purpose of Database Systems`)
在早期，数据库应用程序直接建立在文件系统之上，导致一系列的问题

* **数据冗余和不一致**(`Data redundancy and inconsistency`)

* **数据访问难度大**(`Difficulty in accessing data`)

* **数据隔离**(`Data isolation`)

* **完整性问题**(`Integrity problems`)

    >完整性约束(`Integrity constraints`)问题
    >
    >难以添加新约束和修改约束

* **原子性更新**(`Atomicity of updates`)

    > 更新失败可能会导致数据库的数据处于不一致的状态，或者只更新了部分数据
    >
    > 例如: 从一方转账给另一方，只会有完成转账和完全没发生转账两种情况，不会出现转账方转账了但是收款方未收到款项的问题

* **多用户并发访问**(`Concurrent access by multiple users`)

    > 并发访问需要高性能的支持， 而不受控制的并发访问可能会导致数据不一致

* **安全问题**(`Security problems`)

    > 文件系统难以提供安全保障

    数据库系统就是为了解决这些问题产生的

## 数据模型(`Data Models`)

### 组成



* **一系列用于描述的工具**
- 数据(`Data`)
  
- 数据关系(`Data relationships`)
  - 数据语义(`Data semantics`)
  - 数据约束(`Data constraints`)
  
* **关系模型**(`Relational model`)
* **实体关系数据模型**(`Entity-Relationship data model` 主要用于数据库设计)
* **基于对象的数据模型**(`Object-based data models (Object-oriented and Object-relational)`)
* **半结构化数据模型**(`Semi-structured data model (XML)`)

