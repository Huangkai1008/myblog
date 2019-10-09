---
title: "WSGI ASGI UWSGI的区别"
date: 2019-1-24T13:56:20+08:00
lastmod: 2019-01-24T13:56:20+08:00
description: ""
draft: false
tags: ["python", "network", "http"]
categories: ["python", "network"]
---

## **What is wsgi**
	**CGI(通用网关接口， Common Gateway Interface/CGI)**
	**CGI是定义客户端与web服务器交流方式的程序**。<u>例如正常情况下客户端发来一个请求，根据HTTP协议Web服务器将请求内容解析出来，进过计算后，再将加us安出来的内容封装好，例如服务器返回一个HTML页面，并且根据HTTP协议构建返回内容的响应格式。涉及到TCP连接、HTTP原始请求和相应格式的这些，都由一个软件来完成，这时，以上的工作需要一个程序来完成，而这个程序便是CGI</u>**
	
	
	
	**WSGI(Web服务器网关接口(Python Web Server Gateway Interface，WSGI)**
	
	`WSGI`就是基于`Python`的以`CGI`为标准做一些扩展的协议
	
##  **What is uwsgi**

   `uWSGI`，是指实现了**WSGI协议**的一个**web服务器**。即用来接受客户端请求，转发响应的程序

##  **What is asgi**

    异步网关协议接口，一个介于网络协议服务和`Python`应用之间的标准接口，能够处理多种通用的协议类型，包括`HTTP`，`HTTP2`和`WebSocket`

   `ASGI`尝试保持在一个简单的应用接口的前提下，提供允许数据能够在任意的时候、被任意应用进程发送和接受的抽象。并且同样描述了一个新的，兼容`HTTP`请求响应以及`WebSocket`数据帧的序列格式。允许这些协议能通过网络或本地`socket`进行传输，以及让不同的协议被分配到不同的进程中

##  **Difference between wsgi & asgi**

    1. Wsgi is based on `Http`, not support `websocket`
    2. Asgi is the extension of wsgi.
