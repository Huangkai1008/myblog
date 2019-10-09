# RabbitMQ

* Platform: Centos7

* install Erlang

  ```bash
  yum install erlang
  ```

* install 

  ```bash
  # rpm安装
  wget https://github.com/rabbitmq/rabbitmq-server/releases/download/rabbitmq_v3_6_14/rabbitmq-server-3.6.14-1.el7.noarch.rpm
  yum install rabbitmq-server-3.6.14-1.el7.noarch.rpm
  
  # yum安装
  yum install rabbitmq-server
  ```

* config

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

  

* 服务命令

  ```bash
  systemctl start rabbitmq-server.service     # 启动
  systemctl status rabbitmq-server.service	# 查看状态
  systemctl restart rabbitmq-server.service	# 重启
  systemctl enable rabbitmq-server.service    # 开机自启
  ```

* 访问web页面

  ```
  http://ip:15672
  ```

  