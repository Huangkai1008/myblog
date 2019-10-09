---
title: "Mysql安装"
date: 2018-09-09T13:56:20+08:00
description: ""
draft: false
tags: ["mysql", "install"]
categories: ["mysql"]
---
# Mysql安装
* 版本: 8.0
* 添加源
  ``` bash
  yum local install https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
  ```
* 安装
  ```bash
  yum install mysql-community-server
  ```

* 启动mysql

  ```bash
  systemctl start mysqld
  ```

* 设置mysql开机自启

  ```bash
  systemctl enable mysqld
  ```

* 查看初始密码

  ```bash
   grep 'temporary password' /var/log/mysqld.log
  ```

* 进入mysql

  ```bash
  mysql -u root -p 
  ```

* 修改密码

  ```mysql
  ALTER USER 'root'@'localhost' IDENTIFIED BY 'Huang|12345'
  ```

* 查看版本

  ```mysql
  select version();
  
  +-----------+
  | version() |
  +-----------+
  | 8.0.16    |
  +-----------+
  1 row in set (0.00 sec)
  ```

* 查看端口

  ```mysql
  show global variables like 'port';
  
  +---------------+-------+ 
  | Variable_name | Value | 
  +---------------+-------+ 
  | port          | 3306  | 
  +---------------+-------+ 
  1 row in set (0.04 sec)   
  ```

* 远程访问

  ```mysql
  use mysql;
  update user set host = '%' where user = 'root';
  flush privileges
  ```

  


