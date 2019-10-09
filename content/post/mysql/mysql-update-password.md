---
title: "Mysql修改密码和修改navicat 连接2059问题"
date: 2018-09-12T13:56:20+08:00
lastmod: 2018-09-13T13:56:20+08:00
description: ""
draft: true
tags: ["mysql", "mariadb", "navicat"]
categories: ["mysql"]
---
## Mysql修改密码

* 版本 ：8.0

* Platform: Centos7

* 修改密码
  
  1. 编辑Mysql配置文件, 配置免密码登录
  
     ```bash
     vim /etc/my.cnf
     ```
  
        > 增加<pre>skip-grant-tables</pre>
                                                                              
  
  2. 重启mysql服务
  
     ```bash
     systemctl restart mysqld
     ```
  
  3. 免密码登录mysql, 置空默认root密码
  
     ```bash
     mysql -u root -p
     ```
  
     ```mysql
     use mysql;
     UPDATE user SET authentication_string='' where user='root';  
     ```
  
  4. 删除 /etc/my.cnf 文件最后的 skip-grant-tables
  
  
  5. 修改root用户密码
     ```
     ALTER USER 'root'@'%' IDENTIFIED BY'Huang|12345';
     ```
  
## 修复navicat 2059问题

   1. 更改加密方式

      ```mysql
      ALTER USER 'root'@'%' IDENTIFIED BY 'Huang|12345' PASSWORD EXPIRE NEVER;
      ```

  2. 更改root用户密码

      ```mysql
      ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'Huang|12345';
      ```

  3. 刷新权限

      ```mysql
      FLUSH PRIVILEGES;
      ```

      

      