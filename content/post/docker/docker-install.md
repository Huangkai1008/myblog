---
title: "Docker安装"
date: 2019-07-31T13:56:20+08:00
description: ""
draft: true
tags: ["docker", "install"]
categories: ["docker"]
---
* Platform: centos7

* version: 5.0


## 安装
  * __Uninstall old versions__

    ```bash
    sudo yum remove docker \
                    docker-client \
                    docker-client-latest \
                    docker-common \
                    docker-latest \
                    docker-latest-logrotate \
                    docker-logrotate \
                    docker-engine
    ```


  * __Install Docker CE__

    ```bash
    sudo yum install -y yum-utils \
      device-mapper-persistent-data \
      lvm2
    
    # 设置stable源
    sudo yum-config-manager \
        --add-repo \
        https://download.docker.com/linux/centos/docker-ce.repo
        
    # 安装Docker CE
    sudo yum install docker-ce docker-ce-cli containerd.io
    ```

## 启动
* Docker启动

  ```bash
  sudo systemctl start docker	# 启动Docker
  sudo systemctl status docker	# 查看Docker状态
  ```

    