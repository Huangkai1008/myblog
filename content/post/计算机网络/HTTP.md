---
title: "HTTP"
date: 2020-06-12T12:22:57+08:00
lastmod: 2020-07-16T21:24:19+08:00
description: ""
draft: false
tags: ["计算机网络", "HTTP"]
categories: ["计算机网络"]
---
# HTTP

## 一、概述
> **超文本传输协议（`HyperText Transfer Protocol, HTTP`）** 是 Web 的核心，HTTP 由客户端程序和服务器程序实现

**HTTP 使用 TCP 作为它的支撑运输协议**，因为 HTTP 服务器并不保存关于客户的任何信息，所以 HTTP 是一个**无状态协议（`stateless protocol`）**。


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
  - **协议版本**，通常为  `HTTP/1.1`.
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

### HTTP方法的安全性

如果说一个 HTTP 方法是**安全（`safe`）** 的，是指这是个不会修改服务器的数据的方法。也就是说，这是一个对服务器只读操作的方法。这些方法是安全的：`GET`，`HEAD` 和 `OPTIONS`。所有安全的方法都是幂等的，但并非所有幂等方法都是安全的，例如，`PUT` 和 `DELETE`都是幂等的，但不是安全的。

### HTTP方法的幂等性

一个HTTP方法是**幂等（`idempotent`）** 的，指的是同样的请求被执行一次与连续执行多次的效果是一样的，服务器的状态也是一样的。换句话说就是，幂等方法不应该具有副作用（统计用途除外）。在正确实现的条件下， `GET`， `HEAD`， `PUT` 和 `DELETE`等方法都是**幂等**的，而 `POST`方法不是。所有的 **安全**方法也都是幂等的。

幂等性只与后端服务器的实际状态有关，而每一次请求接收到的状态码不一定相同。例如，第一次调用 `DELETE`方法有可能返回**200**，但是后续的请求可能会返回 **404** 。 `DELETE` 的言外之意是，开发者不应该使用 `DELETE` 法实现具有删除最后条目功能的 RESTful API。

需要注意的是，服务器不一定会确保请求方法的幂等性，有些应用可能会错误地打破幂等性约束。


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
> 多个请求及其响应经过相同的TCP连接发送，此种方式称为使用**持续连接（`persistent connection`）**，也可以称为**长连接**、**连接保活（`keep alive`）**、**连接复用（`connection reuse`）**；

**这里的持续连接（长连接）和非持续连接（短连接）指的都是TCP连接**。



从 **HTTP/1.1** 开始默认使用持续连接，如果要断开连接，需要由客户端或者服务器端提出断开，使用 `Connection : close`；

在 **HTTP/1.1** 之前默认使用非持续连接的，如果需要使用持续连接，则使用 `Connection : Keep-Alive`。

####  非持续连接的问题

* 必须为每一个请求的对象建立和维护一个全新的连接，**会产生大量的开销**，给 web 服务器带来严重负担
* 每一个对象经受两倍 RTT（`Round-Trip Time, RTT, 即往返时延`）的交付时延（一个 RTT 创建 TCP，一个RTT请求和接受一个对象），效率较低

#### 持续连接的问题

持续连接在空闲状态也消耗服务器资源，而且在重负载时，还有可能遭受 [DoS](https://developer.mozilla.org/zh-CN/docs/Glossary/DOS_attack) 攻击，对于这种情况一般采取的策略是：

1. 关闭一些长时间没有发生请求的连接

2. 限制每个客户端的最大连接数，避免恶意的客户端影响服务端


以`Nginx`为例 ：

1. 可以通过 `keepalive_timeout` 参数设置长连接的超时时间，如果在一段时间内连接上没有任何数据收发就主动断开连接，避免空闲连接占用系统资源
2. 可以通过 `keepalive_requests` 参数设置长连接上可发送的最大请求次数。比如设置成 1000，那么当 Nginx 在这个连接上处理了 1000 个请求后，也会主动断开连接

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

```html
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

```html
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

```html
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```



以下为一个Golang实现的简单的使用 Cookie 的代码示例：

{{< gist Huangkai1008 84e3f3dd5f399412e7bdae4a348551fe >}}



### Session

> `Session` 代表着服务器和客户端一次会话的过程。`Session` 对象存储特定用户会话所需的属性及配置信息。这样，当用户在应用程序的 Web 页之间跳转时，存储在 Session 对象中的变量将不会丢失，而是在整个用户会话中一直存在下去。当客户端关闭会话，或者 Session 超时失效时会话结束

### Cookie 和 Session 的不同

* **作用范围不同**，`Cookie` 保存在客户端，`Session` 保存在服务端
* **存取方式的不同**，`Cookie` **只能保存 ASCII**，`Session` 可以存任意数据类型，一般情况下我们可以在 Session 中保持一些常用变量信息
* **有效期不同**，`Cookie` 可设置为长时间保持，比如我们经常使用的默认登录功能，Session 一般失效时间较短，客户端关闭或者 Session 超时都会失效
* **隐私策略不同**，`Cookie` 存储在客户端，比较容易遭到不法获取；`Session` 存储在服务端，安全性相对要好一些
* **存储大小不同**， **单个 `Cookie` 保存的数据不能超过 4KB**，`Session` 可存储上限远高于 `Cookie`

## 七、Web 缓存

> **Web缓存（`Web cache`）**（或 **HTTP 缓存（`HTTP cache`）**）是用于临时存储（缓存）Web文档（如HTML页面和图像），以减少服务器延迟的一种信息技术。Web缓存系统会保存下通过这套系统的文档的副本；如果满足某些条件，则可以由缓存满足后续请求。 Web缓存系统既可以指设备，也可以指计算机程序

### 缓存的种类

缓存的种类有很多,其大致可归为两类：**私有**与**共享缓存**。共享缓存存储的响应能够被多个用户使用。私有缓存只能用于单独用户。

![缓存的种类](https://gitee.com/huanghuang927/picture-host/raw/master/20211113114522.png)

#### **（私有）浏览器缓存**

**私有缓存（`local cache`/`private cache`）** 只能用于单独用户。浏览器缓存拥有用户通过 HTTP 下载的所有文件，可以避免再次向服务器发起多余的请求，也可以提供缓存内容的离线浏览。

#### **（共享）代理缓存**

**共享缓存（`shared cache`/`proxy cache`）** 可以被多个用户使用。例如，ISP 或者公司可能会架设一个 web 代理来作为本地网络基础的一部分提供给用户，这样热门的资源就会被重复使用，减少网络拥堵与延迟。

### 缓存控制

#### Cache-Control

**HTTP/1.1**定义的 [`Cache-Control`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control) 头用来区分对缓存机制的支持情况， 请求头和响应头都支持这个属性。通过它提供的不同的值来定义缓存策略。

##### **禁止进行缓存**

缓存中不得存储任何关于客户端请求和服务端响应的内容。每次由客户端发起的请求都会下载完整的响应内容。

```html
Cache-Control: no-store
```

##### **强制确认缓存**

缓存服务器需要先向源服务器验证缓存资源的有效性，只有当缓存资源有效时才能使用该缓存对客户端的请求进行响应。

```html
Cache-Control: no-cache
```

##### **私有缓存和公共缓存**

**`private`** 指令规定了将资源作为私有缓存，只能被单独用户使用，一般存储在用户浏览器中。

```html
Cache-Control: private
```

**`public`** 指令规定了将资源作为公共缓存，可以被任何中间人（比如中间代理、CDN等）缓存，可以被多个用户使用，一般存储在代理服务器中。

```html
Cache-Control: public
```

##### **过期**

**`max-age`** 指令出现在请求报文，并且缓存资源的缓存时间小于该指令指定的时间，那么就能接受该缓存，

**`max-age`** 指令出现在响应报文，表示缓存资源在缓存服务器中保存的时间：

```html
Cache-Control: max-age=31536000
```

**`Expires`** 首部字段也可以用于告知缓存服务器该资源什么时候会过期：

```html
Expires: Wed, 04 Jul 2012 08:26:05 GMT
```



在**HTTP/1.1**中，会优先处理 **`max-age`** 指令，在**HTTP/1.0**中，会忽略掉 **`max-age`** 指令。

##### **验证方式**

当使用了 **`must-revalidate`** 指令，那就意味着缓存在考虑使用一个陈旧的资源时，必须先验证它的状态，已过期的缓存将不被使用。

```html
Cache-Control: must-revalidate
```

#### 新鲜度(`freshness`)

> 服务端和客户端为资源约定一个过期时间，在该过期时间之前，该资源（缓存副本）就是新鲜的，当过了过期时间后，该资源（缓存副本）则变为陈旧的。驱逐算法用于将陈旧的资源（缓存副本）替换为新鲜的，注意，**一个陈旧的资源（缓存副本）是不会直接被清除或忽略的**

对于含有特定头信息的请求，会去计算缓存寿命。比如`Cache-control: max-age=N`的头，相应的缓存的寿命就是`N`。

缓存失效时间计算公式如下：
$$
expirationTime = responseTime + freshnessLifetime - currentAge
$$
其中，`responseTime` 表示浏览器接收到此响应的时间点。

#### 缓存验证(`validation`)

##### **ETag**

> **`ETag`** 响应头是 URL 的`Entity Tag`，作为一个URL资源的标识符，作为缓存的一种**强校验器**

```html
ETag: "82e22293907ce725faf67773957acd12"
```

当服务端返回资源时，可以根据返回内容计算一个 hash 值或者就是一个数字版本号作为 ETag 的值放到响应首部中，客户端可以在后续的请求的头中可以将缓存资源的 ETag 值放到 [`If-None-Match`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-None-Match) 头首部，服务器收到该请求后，判断缓存资源的 ETag 值和资源的最新 ETag 值是否一致，如果一致则表示缓存资源有效，返回 `304 Not Modified`。

```html
If-None-Match: "82e22293907ce725faf67773957acd12"
```

##### **Last-Modified**

**`Last-Modified`** 响应头可以作为缓存验证的一种**弱校验器**，如果响应头里含有这个信息，客户端可以在后续的请求中带上 [`If-Modified-Since`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-Modified-Since) 来验证缓存。服务器只在所请求的资源在给定的日期时间之后对内容进行过修改的情况下才会将资源返回，状态码为 `200 OK`。如果请求的资源从那时起未经修改，那么返回一个不带有实体主体的 `304 Not Modified` 响应报文。

```html
Last-Modified: Wed, 21 Oct 2015 07:28:00 GMT
```

```html
If-Modified-Since: Wed, 21 Oct 2015 07:28:00 GMT
```

##### Etag 与 Last-Modified的对比

`Etag` 是强校验器，`Last-Modified` 是弱校验器，都同时出现时，**`Etag`的优先级更高**。**`Last-Modified`的精度只能到秒**，如果一个资源频繁修改，用`Last-Modified`并不能区分，而`Etag` 由于每次资源更新时都会生成新的值，会使缓存验证更加准确，缺点是频繁生成的策略可能会额外消耗服务器资源。

### 强制缓存与协商缓存

> **强制缓存**：浏览器不会向服务器发送任何请求，直接从本地缓存中读取文件并返回状态码`200 OK`；
>
> **协商缓存**：浏览器向服务器发送请求，服务器会根据这个请求的请求首部来判断是否命中协商缓存，如果命中，则返回`304 Not Modified`并带上新的响应首部通知浏览器从缓存中读取资源

##### 强制缓存的首部字段

* **`Expires`**
* **`Cache Control`**

##### 协商缓存的首部字段

* **`Etag`**  & **`If-None-Match`**

* **`Last-Modifed`**  & **`If-Modified-Since`**

**强制缓存**和**协商缓存**都存在的情况下，先判断**强制缓存**是否生效，如果生效，不用发起请求，直接用缓存。如果**强制缓存**不生效再发起请求判断**协商缓存**。

## 八、HTTP/1.x 的性能问题及优化方案

虽然HTTP/1.1 的持久连接和管道机制允许复用TCP连接，在一个TCP连接中，也可以同时发送多个请求，但是所有的数据通信都是按次序完成的，服务器只有处理完一个回应，才会处理下一个回应。

比如客户端需要A、B两个资源，管道机制允许浏览器同时发出 A 请求和 B 请求，但服务器还是按照顺序，先回应 A 请求，完成后再回应 B 请求，这样如果前面的回应特别慢，后面就会有很多请求排队等着，这称为**队头阻塞（`Head-of-line blocking`）**。

### 并发连接

**HTTP/1.x** 可以通过对同一个域名发起多个长连接的方式提高通信效率，此种方式称为**并发连接（`concurrent connections`）**。

但是如果客户端滥用并发连接会对带宽和服务器都产生影响，一般来说，现在的浏览器支持的并发连接个数为6 ~ 8个。

### 域名分片

**域名分片（`domain sharding`）** 会将内容拆分到多个子域名中。当使用多个域名来处理多个资源时，浏览器能够同时下载更多资源，从而缩短了页面加载时间并改善了用户体验。

这种方式的问题在于每个域都需要额外的 DNS 查找成本以及建立每个 TCP 连接的开销。

### [HTTP/2](HTTP2.md)

**HTTP/2** 主要是为了改进 **HTTP/1.x** 的性能问题而产生的，在现在，**不推荐使用域名分片的手段提高 HTTP 连接性能**，而是直接升级到 **HTTP/2**。

## 九、HTTPS

### HTTP 的安全性问题

* 使用明文进行通信，内容可能会被窃听
* 不验证通信方的身份，通信方的身份有可能遭遇伪装
* 无法证明报文的完整性，报文有可能遭篡改

### HTTPS 概念

> **超文本传输安全协议**（英语：**H**yper**T**ext **T**ransfer **P**rotocol **S**ecure，缩写 `HTTPS`；常称为 HTTP over TLS、HTTP over SSL 或HTTP Secure），是一种通过计算机网络进行安全通信的协议。
>
> HTTPS 经由 HTTP 进行通信，但利用 SSL/TLS 来加密数据包。
>
> 严格来说，**HTTPS** 并不是单独的协议，而是对工作在一加密连接（TLS 或 SSL）上的常规HTTP协议的称呼。

### HTTPS 作用

#### 加密（Confidentiality）

访问者的连接被加密，隐藏了 URL、cookie 和其他敏感的元数据

#### 认证（Authenticity）

确认访问者正在访问真实网站，而不是与冒充者或通过中间人通信

#### 完整性保护（Integrity）

访问者与网站之间发送的数据未被篡改或修改

###  加密方式

HTTPS 采用混合加密机制

* 使用非对称密钥加密方式，传输对称密钥加密方式所需要的 Secret Key，从而保证安全性
* 获取到 Secret Key 后，再使用对称密钥加密方式进行通信，从而保证效率

#### 对称密钥加密

> **对称密钥加密（`Symmetric-Key Encryption`）**，加密和解密使用同一密钥

#### 非对称密钥加密

> **非对称密钥加密**，又称**公开密钥加密（`Public-Key Encryption`）**，加密和解密使用不同的密钥


## 参考资料

- 【日】户根勤. (2017). 网络是怎样连接的. 人民邮电出版社.
- Kurose, J. F., & Ross, K. W. (2018). *计算机网络-自顶而下方法* (7th ed.). 机械工业出版社.
- [Wikipedia : HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)
- [MDN : HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)
- [MDN : Cookies](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)
- [MDN : HTTP Caching](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)
- [Introduction to HTTPS](https://https.cio.gov/faq/)
