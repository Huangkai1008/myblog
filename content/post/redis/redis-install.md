---
title: "Redis安装配置"
date: 2018-10-11T13:56:20+08:00
lastmod: 2018-10-12T13:56:20+08:00
description: ""
draft: false
tags: ["redis", "install"]
categories: ["redis"]
---


## Redis 安装
* Platform: centos7

* version: 5.0

* 安装

  ```bash
  wget http://download.redis.io/releases/redis-5.0.0.tar.gz # 获取包
  tar -zxvf redis-5.0.0.tar.gz
  mv redis-5.0.0 /usr/local/redis
  make && make install
  ```

## Redis配置
* 设置配置文件目录

  ```bash
  mkdir -p /etc/redis
  cp redis.conf /etc/redis
  ```

* 修改配置文件

  ```bash
  vim /etc/redis/redis.conf
  daemonize yes (no -> yes)   # 守护进程
  bind 0.0.0.0 (127.0.0.1 -> 0.0.0.0) # 远程登录
  protected-mode no (yes -> no)  # 关闭保护模式/或者添加密码
  ```

## Redis使用
* 启动

  ```bash
  /usr/local/bin/redis-server /etc/redis/redis.conf
  ```

* 查看启动

  ```bash
  ps -ef | grep redis
  ```

* 客户端使用

  ```bash
  redis-cli  # 进入
  
  127.0.0.1:6379>set name Huang
  Ok
  redis-cli shutdown # 关闭客户端
  ```

* 开机启动配置

  ```bash
  # 开机启动要配置在 rc.local 中，而 /etc/profile 文件，要有用户登录了，才会被执行。
  echo "/usr/local/bin/redis-server /etc/redis/redis.conf &" >> /etc/rc.local
  ```

  

## Supervisor管理Redis

* 更改redis配置

  ```bash
  vim /etc/redis/redis.conf
  daemonize no (yes -> no)   # 取消守护进程
  ```

* 创建supervisor对redis的配置文件

    ```bash
  vim /etc/supervisord.d/redis.ini
  ```
	
       `redis.ini`文件如下
  
    ```bash
    [program:redis]
	command=redis-server /etc/redis/redis.conf	 	#	启动Redis的命令
	autostart=true							  	  #	  supervisord启动时，该程序也启动
	autorestart=true                                 #   异常退出时，自动启动
	startsecs=3								      #   启动后持续3s后未发生异常，才表示启动成功						
	stdout_logfile=/var/log/supervisor/redis/redis.log  #  标准输出流日志
	stderr_logfile=/var/log/supervisor/redis/redis_err.log	# 标准错误输出流日志
	```
