---
title: "Black"
date: 2018-09-25T13:56:20+08:00
description: ""
draft: true
tags: ["python", "pylint", "format"]
categories: ["python"]
---

## Black

### Install

```bash
pip install black
```

### Configure

* **pyproject.toml**

  ```toml
  [tool.black]
  skip-string-normalization = true  # 禁用双引号风格
  ```

* **pycharm**

  * Create external tools

     > windows: File -> Settings -> Tools -> External Tools
  
     ![External Tools](../images/1569419190770.png)
  
  * Configure file watcher
  
     ![File Watcher](../images/1569421391355.png)
