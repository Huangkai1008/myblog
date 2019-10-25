---
title: "RabbitMQ基础安装使用"
date: 2019-06-12T13:56:20+08:00
lastmod: 2018-06-12T13:56:20+08:00
description: ""
draft: false
tags: ["mq", "rabbitMQ", "install"]
categories: ["mq", "rabbitMQ"]
---
## RabbitMQ

* Platform: Centos7

## 安装

* install Erlang

  ```bash
  yum install erlang
  ```

* install rabbitMQ

  ```bash
  # rpm安装
  wget https://github.com/rabbitmq/rabbitmq-server/releases/download/rabbitmq_v3_6_14/rabbitmq-server-3.6.14-1.el7.noarch.rpm
  yum install rabbitmq-server-3.6.14-1.el7.noarch.rpm
  
  # yum安装
  yum install rabbitmq-server
  ```

## 配置
* 启动远程访问

    ```bash
    [{rabbit, 
      [
      {loopback_users, []}
      ]}]
    ```
  

* 安装插件

  ```bash
  /sbin/rabbitmq-plugins enable rabbitmq_management 
  ```

  
## 使用
* 服务命令

  ```bash
  systemctl start rabbitmq-server.service     # 启动
  systemctl status rabbitmq-server.service	# 查看状态
  systemctl restart rabbitmq-server.service	# 重启
  systemctl enable rabbitmq-server.service    # 开机自启
  ```

* 添加用户
  ```bash
  rabbitmqctl add_user root root123 # 添加新用户，用户名为 "root" ，密码为 "root123"
  rabbitmqctl set_permissions -p / root ".*" ".*" ".*" # 为root用户添加所有权限
  rabbitmqctl set_user_tags root adminstrator  # 设置root 用户为管理员角色
  ```

* 访问web页面

  ```
  http://ip:15672
  ```

  