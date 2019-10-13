---
title: "Poetry安装和使用"
date: 2019-09-14T13:56:20+08:00
lastmod: 2019-10-13T10:10:16+08:00
description: ""
draft: false
tags: ["python", "python3", "venv", "virtual environment"]
categories: ["python"]
---


## 安装
* **custom installer**
  ```bash
  curl -sSL https://raw.githubusercontent.com/sdispater/poetry/master/get-poetry.py | python
  ```

* **pip**

  ```bash
  pip install poetry  # 不推荐, 可能会有冲突
  ```

**验证安装**
```bash
poetry --version
```

## 使用

* **项目初始化**
  
* 从pipenv/pip等工具迁移
  
	```bash
    poetry init   # 进入交互式命令行填写项目信息, 会生成pyproject.toml
	```
  
* **添加依赖**

  * 添加包

    ```bash
    poetry add 
    poetry add fastapi=0.38.1 -E all #  pipenv install fastapi[all]
    poetry add celery --extras "librabbitmq redis auth msgpack"  # pip install "celery[librabbitmq,redis,auth,msgpack]"
    ```

  * 依赖安装

    ```bash
    poetry install  # 会从pyproject.toml文件里读取, 如果有poetry.lock文件则会从lock文件中读取锁定依赖并安装
    ```

  * 虚拟环境地址

     -  **windows10**: $User\AppData\Local\pypoetry\Cache\virtualenvs

## 配置

*  **添加源**

    修改pyproject.toml

     ```toml
     [[tool.poetry.source]]
     name = "tsinghua"
     url = "https://pypi.tuna.tsinghua.edu.cn/simple/"
     verify_ssl = true
     ```






