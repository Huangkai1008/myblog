---
title: "Mysql优化"
date: 2018-11-06T13:56:20+08:00
lastmod: 2019-11-11T13:56:20+08:00
description: ""
draft: false
tags: ["mysql", "optimize"]
categories: ["mysql"]
---

## mysql中min和max查询优化

> `max()`函数需扫描where条件过滤后的所有行

> 慎用`max()`函数，特别是频繁执行的sql，若需用到可转化为`order by id desc limit 1`