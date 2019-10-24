---
title: "Python安装"
date: 2017-01-08T22:49:14+08:00
lastmod: 2017-01-08T22:49:14+08:00
description: ""
draft: false
tags: ["python", "install"]
categories: ["python"]
---

## Python安装  
* Platform: centos7

* Version: 3.7

* 安装编译环境
    ```bash
    yum install zlib-devel bzip2-devel  openssl-devel ncurses-devel libffi-devel
    ```

* 下载
    ```bash
    wget --no-check-certificate https://www.python.org/ftp/python/3.7.4/Python-3.7.4.tgz
    ```
* 创建安装目录解压
    ```bash
    sudo mkdir /usr/local/python3
  
    tar -zxvf Python-3.7.4.tgz

    cd Python-3.7.4/
    ``` 

* 编译安装
    ```bash
    sudo ./configure --prefix=/usr/local/python3 # 指定创建的目录
  
    make && make install # 编译安装
    ```


## 软链接
* 创建python和pip软链接
    ```bash
    ln -s /usr/local/python3/bin/python3 /usr/bin/python3  # python3 软链接
    ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3  # pip3软链接
    ln -s /usr/local/python3/bin/pipenv /usr/bin/pipenv  # pipenv软链接
    ```