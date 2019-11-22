---
title: "日志库Loguru使用教程"
date: 2019-11-22T15:19:35+08:00
lastmod: 2019-11-22T15:19:35+08:00
description: ""
draft: false
tags: ["python", "logging", "logger"]
categories: ["python"]
---
Loguru是一个好用的第三方python日志库

## 安装
```bash
pip install loguru
```

## 初步使用
### 添加日志到标准输出流
```python
import sys

from loguru import logger

logger.add(sys.stderr, format='{time} {level} {message}', filter='my module', level='INFO')
```

### 添加日志到文件
```python
from loguru import logger

logger.add('file_1.log', rotation='500 MB')    # Automatically rotate too big file
logger.add("file_2.log", rotation='12:00')     # New file is created each day at noon
logger.add("file_3.log", rotation="1 week")    # Once the file is too old, it's rotated

logger.add("file_X.log", retention="10 days")  # Cleanup after some time

logger.add("file_Y.log", compression="zip")    # Save some loved space
```

### 捕获异常
```python
from loguru import logger

@logger.catch
def my_function(x, y, z):
    # An error? It's caught anyway!
    return 1 / (x + y + z)
```

### 为日志添加颜色
```python
import sys

from loguru import logger

logger.add(sys.stdout, colorize=True, format="<green>{time}</green> <level>{message}</level>")

```

### 异步、线程安全、多进程安全
```python
from loguru import logger

logger.add("file.log", enqueue=True)
```

### 完全描述异常
> 记录代码中发生的异常对于跟踪错误很重要，但是如果您不知道为什么失败，则记录日志就毫无用处。
> Loguru通过允许显示整个堆栈跟踪（包括变量值）来帮助您发现问题

```python
from loguru import logger

logger.add("output.log", backtrace=True, diagnose=True)  # Set 'False' to not leak sensitive data in prod

```

## 配置到flask
```python
import logging
import sys

from pathlib import Path

from flask import Flask
from loguru import logger

app = Flask(__name__)

class InterceptHandler(logging.Handler):
    def emit(self, record):
        logger_opt = logger.opt(depth=6, exception=record.exc_info)
        logger_opt.log(record.levelname, record.getMessage())



def configure_logging(flask_app: Flask):
    """配置日志"""
    path = Path(flask_app.config['LOG_PATH'])
    if not path.exists():
        path.mkdir(parents=True)
    log_name = Path(path, 'sips.log')


    logging.basicConfig(handlers=[InterceptHandler(level='INFO')], level='INFO')
    logger.configure(handlers=[{"sink": sys.stderr, "level": 'INFO'}])  # 配置日志到标准输出流
    logger.add(
        log_name, rotation="500 MB", encoding='utf-8', colorize=False, level='INFO'
    )   # 配置日志到输出到文件
```


