## Golang Install

* Platform: centos7

* version: 1.12

* 安装

  ```bash
  cd /opt
  wget https://studygolang.com/dl/golang/go1.12.4.linux-amd64.tar.gz
  tar xzvf go1.10.3.linux-amd64.tar.gz	# 安装
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

  