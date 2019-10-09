---
title: "MariaDB安装"
date: 2018-09-09T13:56:20+08:00
description: ""
draft: false
tags: ["mysql", "install", "mariadb"]
categories: ["mysql"]
---

## MariaDB Install

* **platform**: Centos7

* **Install**

  ```bash
  yum install -y mariadb-server
  ```

* **Using**

  ```bash
  systemctl start mariadb.service # 启动
  systemctl enable mariadb.service # 开机自启
  ```

* **Configure**

    ```bash
    mysql_secure_installation
    ```

  1. 首先是设置密码，会提示先输入密码

     > Enter current password for root (enter for none): <–直接回车
     > Set root password? [Y/n]  <– 是否设置root用户密码，输入y并回车或直接回车
     > New password:  <– 设置root用户的密码
     > Re-enter new password: <– 再输入一次你设置的密码
     > 其他配置
     > Remove anonymous users? [Y/n]  <– 是否删除匿名用户，Y回车
     > Disallow root login remotely? [Y/n]  <–是否禁止root远程登录, N回车,
     > Remove test database and access to it? [Y/n]  <– 是否删除test数据库，Y回车
     > Reload privilege tables now? [Y/n]  <– 是否重新加载权限表，Y回车


  2.开启远程访问

    ```mysql
    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'IDENTIFIED BY '123456' WITH GRANT 	   OPTION;
    ```

  3. 刷新权限
    ```mysql
    flush privileges
    ```

  4. 配置文件地址

    /etc/my.cnf.d/mysql-clients.cnf
