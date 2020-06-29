---
title: "HTTP"
date: 2020-06-12T12:22:57+08:00
lastmod: 2020-06-29T19:47:57+08:00
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

![HTTP 1.X 的连接类型](https://gitee.com/huanghuang927/picture-host/raw/master/20211030124313.png)

### 非持续连接和持续连接

> 每个请求及其响应对经一个单独的 TCP 连接发送，此种方式称为使用**非持续连接（`non-persistent connection`）**，也可以称为**短连接**；
>
> 多个请求及其响应经过相同的TCP连接发送，此种方式称为使用**持续连接（`persistent connection`）**，也可以称为**长连接**；

**这里的持续连接（长连接）和非持续连接（短连接）指的都是TCP连接**。



从 **HTTP/1.1** 开始默认使用持续连接，如果要断开连接，需要由客户端或者服务器端提出断开，使用 `Connection : close`；

在 **HTTP/1.1** 之前默认使用非持续连接的，如果需要使用持续连接，则使用 `Connection : Keep-Alive`。

####  非持续连接的问题

* 必须为每一个请求的对象建立和维护一个全新的连接，**会产生大量的开销**，给 web 服务器带来严重负担
* 每一个对象经受两倍 RTT（`Round-Trip Time, RTT, 即往返时延`）的交付时延（一个 RTT 创建 TCP，一个RTT请求和接受一个对象），效率较低

#### 持续连接的问题

* 在空闲状态也消耗服务器资源，而且在重负载时，还有可能遭受 [DoS](https://developer.mozilla.org/zh-CN/docs/Glossary/DOS_attack) 攻击，对于这种情况一般采取的策略是：
  1.关闭一些长时间没有发生请求的连接
  2.限制每个客户端的最大连接数，避免恶意的客户端影响服务端

## 六、Cookie 和 Session

### HTTP Cookies

> **HTTP Cookie（也叫 Web Cookie 或浏览器 Cookie）** 是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。**Cookie 使基于[无状态](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview#http_is_stateless_but_not_sessionless)的 HTTP 协议记录稳定的状态信息成为了可能**

Cookie 曾一度用于客户端数据的存储，因当时并没有其它合适的存储办法而作为唯一的存储手段，但现在随着现代浏览器开始支持各种各样的存储方式，Cookie 渐渐被淘汰。由于服务器指定 Cookie 后，浏览器的每次请求都会携带 Cookie 数据，会带来额外的性能开销（尤其是在移动环境下）。新的浏览器API已经允许开发者直接将数据存储到本地，如使用 [Web storage API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API) （本地存储和会话存储）或 [IndexedDB](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API) 。

#### 用途

* **会话状态管理**（如用户登录状态、购物车、游戏分数或其它需要记录的信息）

* **个性化设置**（如用户自定义设置、主题等）

* **浏览器行为跟踪**（如跟踪分析用户行为等）


#### 创建过程
当服务器收到 HTTP 请求时，服务器可以在响应头里面添加一个 Set-Cookie 选项。浏览器收到响应后通常会保存下 Cookie，之后对该服务器每一次请求中都通过  Cookie 请求头部将 Cookie 信息发送给服务器。另外，Cookie 的过期时间、域、路径、有效期、适用站点都可以根据需要来指定。

##### **Set-Cookie响应头部和Cookie请求头部**

服务器使用 [`Set-Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie) 响应头部向用户代理（一般是浏览器）发送 Cookie信息。一个简单的 Cookie 可能像这样：

```http
Set-Cookie: <cookie名>=<cookie值>
```



服务器通过该头部告知客户端保存 Cookie 信息，客户端得到响应报文后把 Cookie 内容保存到浏览器中：

```http
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[page content]
```

  

现在，对该服务器发起的每一次新请求，浏览器都会将之前保存的Cookie信息通过 Cookie 请求首部字段再发送给服务器：

```http
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```



#### 生命周期

* **会话期 Cookie**：浏览器关闭之后它会被自动删除，也就是说它仅在会话期内有效
* **持久性 Cookie**：指定过期时间（Expires）或有效期（max-age）之后就成为了持久性的 Cookie

```http
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```



#### 作用域

##### **Domain 标识**

`Domain`标识指定了哪些主机可以接受 Cookie。如果不指定，默认为当前文档的主机（不包含子域名）。如果指定了 Domain，则一般包含子域名。例如，如果设置 Domain=mozilla.org，则 Cookie 也包含在子域名中（如 developer.mozilla.org）。

##### **Path 标识**

`Path` 标识指定了主机下的哪些路径可以接受 Cookie（该 URL 路径必须存在于请求 URL 中）。以字符 %x2F ("/") 作为路径分隔符，子路径也会被匹配。例如，设置 Path=/docs，则以下地址都会匹配：

- /docs
- /docs/Web/
- /docs/Web/HTTP



#### 限制访问

##### **Secure 属性**

标记为 `Secure` 的 Cookie 只应通过被 HTTPS 协议加密过的请求发送给服务端，因此可以预防**中间人**的攻击。但即便设置了 `Secure` 标记，敏感信息也不应该通过 Cookie 传输，因为 Cookie 有其固有的不安全性，`Secure` 标记也无法提供确实的安全保障, 例如，可以访问客户端硬盘的人可以读取它。

##### **HttpOnly 属性**

标记为 `HttpOnly` 的 Cookie 不能被 JavaScript 脚本调用。**跨站脚本攻击 (XSS)** 常常使用 JavaScript 的 `document.cookie` API 窃取用户的 Cookie 信息，因此使用 HttpOnly 标记可以在一定程度上避免 XSS 攻击。

示例：

```http
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```



以下为一个Golang实现的简单的使用 Cookie 的代码示例：

{{< gist Huangkai1008 84e3f3dd5f399412e7bdae4a348551fe >}}



## 参考资料

- 【日】户根勤. (2017). 网络是怎样连接的. 人民邮电出版社.
- Kurose, J. F., & Ross, K. W. (2018). *计算机网络-自顶而下方法* (7th ed.). 机械工业出版社.
- [Wikipedia : HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)
- [MDN : HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)
- [MDN: Cookies](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)

