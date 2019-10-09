---
title: "Black安装和使用"
date: 2019-09-27T13:56:20+08:00
lastmod: 2019-09-27T13:56:20+08:00
description: ""
draft: false
tags: ["python", "pylint", "format"]
categories: ["python"]
---

## Black

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
  
     ![External Tools](../../../images/1569419190770.png)
  
  * Configure file watcher
  
     ![File Watcher](../../../images/1569421391355.png)
