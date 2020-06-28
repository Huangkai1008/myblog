---
title: "HTTP"
date: 2020-06-12T12:22:57+08:00
lastmod: 2020-06-12T12:22:57+08:00
description: ""
draft: false
tags: ["计算机网络"]
categories: ["计算机网络"]
---
# HTTP

## 一、概述
> **超文本传输协议（`HyperText Transfer Protocol, HTTP`）** 是 Web 的核心，HTTP 由客户端程序和服务器程序实现

HTTP 使用 TCP 作为它的支撑运输协议，因为 HTTP 服务器并不保存关于客户的任何信息，所以 HTTP 是一个**无状态协议（`stateless protocol`）**。


### 请求和响应报文

客户端发送一个请求报文给服务器，服务器根据请求报文中的信息进行处理，并将处理结果放入响应报文中返回给客户端。

#### **请求消息（`requests`）**

 ```http
GET / HTTP/1.1
Host: developer.mozilla.org
Accept-Language: fr
 ```

- **起始行（`start line`）**：包含一个[HTTP方法（`method`）](#二、HTTP方法)、**请求目标（`request target`）** 和 **HTTP 版本 （`HTTP version`）**
- **消息头（`headers`）**： 整个 header（包括其值）表现为单行形式
- 一个空行用来分隔首部和内容主体 Body
- **消息主体（`body`）**

  ![HTTP Requests Example](https://gitee.com/huanghuang927/picture-host/raw/master/20210906175921.png)
  
  ----------------

#### **响应消息(`responses`)**

```http
HTTP/1.1 200 OK
Date: Sat, 09 Oct 2010 14:28:02 GMT
Server: Apache
Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT
ETag: "51142bc1-7449-479b075b2891b"
Accept-Ranges: bytes
Content-Length: 29769
Content-Type: text/html

<!DOCTYPE html... (here comes the 29769 bytes of the requested web page)
```

- **状态行（`status line`)**：
  - 协议版本，通常为  `HTTP/1.1`.
  - **状态码 (`status code`)**，表明请求是成功或失败。常见的状态码是 [`200`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200)，[`404`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/404)，或 [`302`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/302)
  - **状态文本 (`status text`)**：一个简短的，纯粹的信息，通过状态码的文本描述，帮助理解该 HTTP 消息
- **消息头（`Headers`）**： 整个 header（包括其值）表现为单行形式
- **一个空行**用来分隔首部和内容主体 Body
- **消息主体（`body`）**

![HTTP Responses Example](https://gitee.com/huanghuang927/picture-host/raw/master/20210906205815.png)

-------------------------

## 二、HTTP 方法

|  请求方法   |                             描述                             |                             RFC                              | 请求具有请求实体 | 响应具有响应实体 | 安全方法 | 是否幂等 |
| :---------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :--------------: | :--------------: | :------: | :------: |
|   **GET**   |                      请求一个指定的资源                      | [RFC](https://en.wikipedia.org/wiki/RFC_(identifier)) [7231](https://datatracker.ietf.org/doc/html/rfc7231) |       可选       |        是        |    是    |    是    |
|  **HEAD**   | 获取报文首部，不返回报文实体主体，主要用于确认 URL 的有效性以及资源更新的日期时间等 | [RFC](https://en.wikipedia.org/wiki/RFC_(identifier)) [7231](https://datatracker.ietf.org/doc/html/rfc7231) |       可选       |        否        |    是    |    是    |
|  **POST**   |                  用于将实体提交到指定的资源                  | [RFC](https://en.wikipedia.org/wiki/RFC_(identifier)) [7231](https://datatracker.ietf.org/doc/html/rfc7231) |        是        |        是        |    否    |    否    |
|   **PUT**   |                 向指定资源位置上传其最新内容                 | [RFC](https://en.wikipedia.org/wiki/RFC_(identifier)) [7231](https://datatracker.ietf.org/doc/html/rfc7231) |        是        |        是        |    否    |    是    |
|  **PATCH**  |                      对资源进行部分修改                      | [RFC](https://en.wikipedia.org/wiki/RFC_(identifier)) [5789](https://datatracker.ietf.org/doc/html/rfc5789) |        是        |        是        |    否    |    否    |
| **DELETE**  |                        删除指定的资源                        | [RFC](https://en.wikipedia.org/wiki/RFC_(identifier)) [7231](https://datatracker.ietf.org/doc/html/rfc7231) |       可选       |        是        |    否    |    是    |
| **CONNECT** |               要求在与代理服务器通信时建立隧道               | [RFC](https://en.wikipedia.org/wiki/RFC_(identifier)) [7231](https://datatracker.ietf.org/doc/html/rfc7231) |       可选       |        是        |    否    |    否    |
| **OPTIONS** |                查询指定的 URL 能够支持的方法                 | [RFC](https://en.wikipedia.org/wiki/RFC_(identifier)) [7231](https://datatracker.ietf.org/doc/html/rfc7231) |       可选       |        是        |    是    |    是    |
|  **TRACE**  |                服务器会将通信路径返回给客户端                | [RFC](https://en.wikipedia.org/wiki/RFC_(identifier)) [7231](https://datatracker.ietf.org/doc/html/rfc7231) |        否        |        是        |    是    |    是    |

[^注]: HTTP版本为1.1

------------

## 三、HTTP 首部（`header`)

|             类型             |                   描述                   |                      实例                      |
| :--------------------------: | :--------------------------------------: | :--------------------------------------------: |
| **通用头（`General headers`）**  |       适用于请求和响应信息的头字段       |             `Date`,`Cache-Control`             |
| **请求头（`Request headers`）**  |    用于表示请求信息的附加信息的头字段    | `Authorization`,`User-Agent`,`Accept-Encoding` |
| **响应头（`Response headers`）** |    用于表示响应信息的附加信息的头字段    |              `Location`,`Server`               |
|  **实体头（`Entity headers`）**  | 用于表示实体（消息体）的附加信息的头字段 |  `Allow`,`Content-Encoding`,`Expires`, `Etag`  |

## 四、HTTP 状态码（`status code`）

|            状态码             |           含义           |
| :---------------------------: | :----------------------: |
| 1xx(*informational response*) | 告知请求的处理进度和情况 |
|       2xx(*successful*)       |           成功           |
|      3xx(*redirection*)       |      需要进一步处理      |
|      4xx(*client error*)      |        客户端错误        |
|      5xx(*server error*)      |        服务器错误        |

## 五、连接管理

### 非持续连接和持续连接

> 每个请求及其响应对经一个单独的 TCP 连接发送，此种方式称为使用**非持续连接（`non-persistent connection`）**，也可以称为**短连接**；
>
> 多个请求及其响应经过相同的TCP连接发送，此种方式称为使用**持续连接（`persistent connection`）**，也可以称为**长连接**

从 **HTTP/1.1** 开始默认使用持续连接，如果要断开连接，需要由客户端或者服务器端提出断开，使用 `Connection : close`；

在 **HTTP/1.1** 之前默认是非持续连接的，如果需要使用持续连接，则使用 `Connection : Keep-Alive`。

####  非持续连接的问题

* 必须为每一个请求的对象建立和维护一个全新的连接，**会产生大量的开销**，给 web 服务器带来严重负担
* 每一个对象经受两倍 RTT（`Round-Trip Time, RTT, 即往返时延`）的交付时延（一个 RTT 创建 TCP，一个RTT请求和接受一个对象），效率较低

#### 持续连接的问题

* 在空闲状态也消耗服务器资源，而且在重负载时，还有可能遭受 [DoS](https://developer.mozilla.org/zh-CN/docs/Glossary/DOS_attack) 攻击，对于这种情况一般采取的策略是：
  1.关闭一些长时间没有发生请求的连接
  2.限制每个客户端的最大连接数，避免恶意的客户端影响服务端

## 参考资料

- 【日】户根勤. (2017). 网络是怎样连接的. 人民邮电出版社.
- Kurose, J. F., & Ross, K. W. (2018). *计算机网络-自顶而下方法* (7th ed.). 机械工业出版社.
- [Wikipedia : HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)
- [MDN : HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)
