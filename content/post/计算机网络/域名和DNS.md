---
title: "域名和DNS"
date: 2020-06-14T16:02:15+08:00
lastmod: 2020-06-14T16:02:15+08:00
description: ""
draft: false
tags: ["计算机网络"]
categories: ["计算机网络"]
---
# DNS 和域名

## 一、域名

**网域名称**（英语：Domain Name，简称：Domain），简称**域名**、**网域**，是由一串用点分隔的字符组成的互联网上某一台计算机或计算机组的名称，用于在数据传输时标识计算机的电子方位。域名可以说是一个[IP](IP.md)地址的代称，目的是为了便于记忆后者。

### 域名的层级

`www.example.com` 真正的域名是 `www.example.com.root` ，简写为 `www.example.com.` 。因为，根域名 `.root` 对于所有域名都是一样的，所以平时是省略的。



根域名的下一级，叫做**顶级域名（`top-level domain，缩写为TLD`）**，比如 `.com` 、 `.net` ；



再下一级叫做**次级域名（`second-level domain，缩写为SLD`）**，比如 `www.example.com` 里面的 `.example` ，这一级域名是用户可以注册的；



再下一级是**主机名（`host`）**，比如`www.example.com`里面的`www`，又称为**三级域名**，这是用户在自己的域里面为服务器分配的名称，是用户可以任意分配的。

> 主机名.次级域名.顶级域名.根域名
>
> host.sld.tld.root

![](https://gitee.com/huanghuang927/picture-host/raw/master/20211212155532.png)

## 二、DNS

**域名系统**（英语：**D**omain **N**ame **S**ystem，缩写：**DNS**）是一个分布式数据库，提供了**域名**和 [IP](IP.md)地址之间相互转换的服务。

### 查询过程

DNS 服务器根据[域名的层级](#域名的层级)，进行**分级查询**。

每一级域名都有自己的**NS（`Name Server`）** 记录，NS记录指向该级域名的域名服务器。这些服务器知道下一级域名的各种记录。

> "分级查询"，就是从根域名开始，依次查询每一级域名的NS记录，直到查到最终的IP地址
>
> 过程大致为:
>
> 1. 从"根域名服务器"查到"顶级域名服务器"的NS记录和A记录（IP地址）
> 2. 从"顶级域名服务器"查到"次级域名服务器"的NS记录和A记录（IP地址）
> 3. 从"次级域名服务器"查出"主机名"的IP地址

```bash
dig math.stackexchange.com

; <<>> DiG 9.11.3-1ubuntu1.15-Ubuntu <<>> math.stackexchange.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 50719
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;math.stackexchange.com.  IN A

;; ANSWER SECTION:
math.stackexchange.com. 600 IN A 151.101.193.69
math.stackexchange.com. 600 IN A 151.101.129.69
math.stackexchange.com. 600 IN A 151.101.65.69
math.stackexchange.com. 600 IN A 151.101.1.69

;; Query time: 74 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Mon Sep 13 10:18:12 UTC 2021
;; MSG SIZE  rcvd: 115


```

### 记录类型

域名与IP之间的对应关系，称为**记录（`record`）**。根据使用场景，**记录**可以分成不同的**类型（`type`）**。

|               记录类型               | 记录类型简称 |                             描述                             |
| :----------------------------------: | :----------: | :----------------------------------------------------------: |
|      **地址记录（`Address`）**       |     `A`      |                     返回域名指向的IP地址                     |
| **域名服务器记录（`Name Server`）**  |     `NS`     | 返回保存下一级域名信息的服务器地址。该记录只能设置为域名，不能设置为IP地址 |
|   **邮件记录（`Mail eXchange`）**    |     `MX`     |                 返回接收电子邮件的服务器地址                 |
| **规范名称记录（`Canonical Name`）** |   `CNAME`    |      返回另一个域名，即当前查询的域名是另一个域名的跳转      |
| **逆向查询记录（`Pointer Record`）** |    `PTR`     |                    只用于从IP地址查询域名                    |

### 传输方式

DNS 可以使用 **UDP** 或者 **TCP** 进行传输，**使用的端口号都为 53**。大多数情况下 DNS 使用 UDP 进行传输，这就要求域名解析器和域名服务器都必须自己处理超时和重传从而保证可靠性。在两种情况下会使用 TCP 进行传输：

- 如果返回的响应超过的 512 字节（UDP 最大只支持 512 字节的数据）
- 区域传送（区域传送是主域名服务器向辅助域名服务器传送变化的那部分数据）

## 参考资料

- 【日】户根勤. (2017). 网络是怎样连接的. 人民邮电出版社.
- [Wikipedia : DNS](https://en.wikipedia.org/wiki/Domain_Name_System)
- [阮一峰的网络日志: DNS 原理入门](https://www.ruanyifeng.com/blog/2016/06/dns.html)
