---
title: "Eventlet使用"
date: 2019-11-22T11:20:20+08:00
lastmod: 2019-11-22T11:20:20+08:00
description: ""
draft: false
tags: ["python", "greenlet"]
categories: ["python"]
---
Evenlet是一个Python的基于携程的网络库，它改变了你代码运行的方式，但是没有改变你怎么写代码

## 安装
```bash
pip install eventlet
```

## 简单使用
### 从eventlet.green导入相关库
```python
import eventlet
from eventlet.green import urllib2

urls = [
    "https://www.google.com/intl/en_ALL/images/logo.gif",
    "http://python.org/images/python-logo.gif",
    "http://us.i1.yimg.com/us.yimg.com/i/ww/beta/y3.gif",
]

def fetch(url):
    print("opening", url)
    body = urllib2.urlopen(url).read()
    print("done with", url)
    return url, body

pool = eventlet.GreenPool(200)
for url, body in pool.imap(fetch, urls):
    print("got body from", url, "of length", len(body))
```

### 使用spawn使用协程
```python
import time

import eventlet


def green_thread_1(num):
    eventlet.greenthread.sleep(1)
    print(f'green_thread_1 get result {num}')
    return x


def green_thread_2(num):
    eventlet.greenthread.sleep(2)
    print(f'green_thread_2 get result {num}')
    return y


time1 = time.perf_counter()
x = eventlet.spawn(green_thread_1, 1)
y = eventlet.spawn(green_thread_2, 2)
x.wait()
y.wait()
time2 = time.perf_counter()
print(time2 - time1)
>>> green_thread_1 get result 1
    green_thread_2 get result 2
    2.0049271
```

> `spawn`函数产生的协程可以通过`wait`函数来执行并获取返回结果， 如上例子中， 使用绿色线程的休眠模拟io操作的耗时,
> 程序就会切换到下一个协程，切换协程由调度器决定


### 使用monkey-patch
```python
from eventlet import monkey_patch
from eventlet import GreenPool


green_pool = GreenPool()
monkey_patch()


def producer():
    pass

def consumer():
    pass

green_pool.spawn(producer)
green_pool.spawn(consumer)
green_pool.waitall()
```

### 和gunicorn一起使用
以flask应用为例
```bash
gunicorn --worker-class eventlet -b 0.0.0.0:5000 -w 1 run:app
```