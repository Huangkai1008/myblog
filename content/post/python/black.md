---
title: "Black安装和使用"
date: 2019-09-27T13:56:20+08:00
lastmod: 2019-09-27T13:56:20+08:00
description: ""
draft: false
tags: ["python", "pylint", "format"]
categories: ["python"]
---

Black是一个毫不妥协的python代码格式化工具, 特点是可配置项较少
Black依赖于python3.6+, 官方地址在https://github.com/psf/black

## Install

```bash
pip install black
```

## Configure

* **pyproject.toml**

  ```toml
  [tool.black]
  skip-string-normalization = true  # 禁用双引号风格
  ```

* **pycharm**

  * Create external tools

     > windows: File -> Settings -> Tools -> External Tools
  
     ![External Tools](https://blog-1259169620.cos.ap-guangzhou.myqcloud.com/img/20210413134138.png)
  
  * Configure file watcher
  
     ![File Watcher](https://blog-1259169620.cos.ap-guangzhou.myqcloud.com/img/20210413134203.png)
