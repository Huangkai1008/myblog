---
title: "Golang安装"
date: 2018-07-31T13:56:20+08:00
lastmod: 2018-07-31T13:56:20+08:00
description: ""
draft: false
tags: ["golang", "install"]
categories: ["golang"]
---

## 安装

* Platform: Centos7

* version: 1.12

* 安装

  ```bash
  cd /opt
  wget https://studygolang.com/dl/golang/go1.12.4.linux-amd64.tar.gz
  tar xzvf go1.12.4.linux-amd64.tar.gz	# 安装
  ```

* 配置环境变量

  ```bash
  vim ~/.zshrc	# 如果用bash就是vim ~/.bashrc
  
  # 追加golang配置
  export GOROOT=/opt/go
  export PATH=$PATH:$GOROOT/bin
  # 立即生效
  source ~/.zshrc
  # 查看版本
  go version
  ```

  