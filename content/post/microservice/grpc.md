---
title: "gRPC教程"
date: 2019-03-21T13:56:20+08:00
lastmod: 2019-07-31T13:56:20+08:00
description: ""
draft: true
tags: ["grpc", "微服务", "micro-service"]
categories: ["grpc", "微服务"]
---
## 概念
在 gRPC 里客户端应用可以像调用本地对象一样直接调用另一台不同的机器上服务端应用的方法，使得您能够更容易地创建分布式应用和服务。与许多 RPC 系统类似，gRPC 也是基于以下理念：定义一个服务，指定其能够被远程调用的方法（包含参数和返回类型）。在服务端实现这个接口，并运行一个 gRPC 服务器来处理客户端调用。在客户端拥有一个存根能够像服务端一样的方法

![gRPC concept](https://gitee.com/huanghuang927/picture-host/raw/master/20210413133854.png)

1、客户端（gRPC Stub）调用 A 方法，发起 RPC 调用。

2、对请求信息使用 Protobuf 进行对象序列化压缩（IDL）。

3、服务端（gRPC Server）接收到请求后，解码请求体，进行业务逻辑处理并返回。

4、对响应结果使用 Protobuf 进行对象序列化压缩（IDL）。

5、客户端接受到服务端响应，解码请求体。回调被调用的 A 方法，唤醒正在等待响应（阻塞）的客户端调用并返回响应结果。


## 数据格式
gRPC默认使用protocol buffers, 也可以使用json之类的其他数据格式
