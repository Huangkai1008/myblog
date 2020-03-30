---
title: "Python线程池使用"
date: 2018-02-08T22:49:14+08:00
lastmod: 2018-02-08T22:49:14+08:00
description: ""
draft: false
tags: ["python", "multi-threading"]
categories: ["python"]
---
## 介绍
从Python3.2开始，标准库为我们提供了 concurrent.futures 模块，它提供了 ThreadPoolExecutor (线程池)和ProcessPoolExecutor (进程池)两个类。

相比 threading 等模块，该模块通过 submit 返回的是一个 future 对象，它是一个未来可期的对象，通过它可以获悉线程的状态主线程(或进程)中可以获取某一个线程(进程)执行的状态或者某一个任务执行的状态及返回值：

1.主线程可以获取某一个线程（或者任务的）的状态，以及返回值。

2.当一个线程完成的时候，主线程能够立即知道。

3.让多线程和多进程的编码接口一致。

## 基本使用

```python        
from concurrent.futures import ThreadPoolExecutor
import time


def get_page(url):
    time.sleep(url)
    return url


with ThreadPoolExecutor(max_workers=5) as t:  # 创建一个最大容纳数量为5的线程池
    task1 = t.submit(get_page, 1)
    task2 = t.submit(get_page, 2)  # 通过submit提交执行的函数到线程池中
    task3 = t.submit(get_page, 3)

    print(f"task1: {task1.done()}")  # 通过done来判断线程是否完成
    print(f"task2: {task2.done()}")
    print(f"task3: {task3.done()}")

    time.sleep(2.5)
    print(f"task1: {task1.done()}")
    print(f"task2: {task2.done()}")
    print(f"task3: {task3.done()}")
    print(task1.result())  # 通过result来获取返回值

>>> task1: False
    task2: False
    task3: False 
    ...
    task1: True
    task2: True
    task3: False
```

## Api
### as_completed
> **concurrent.futures.as_completed(fs, timeout=None)**

> 返回一个生成器在迭代过程中会阻塞

> 直到线程完成或者异常时,返回一个被set_result的Future对象

> 此方法的返回顺序为哪个线程先失败/完成就返回

```python
from concurrent.futures import ThreadPoolExecutor, as_completed
import time


def get_page(url):
    time.sleep(url)
    return url


with ThreadPoolExecutor(max_workers=5) as t:  # 创建一个最大容纳数量为5的线程池
    tasks = [t.submit(get_page, page) for page in range(1, 5)]
    
    for future in as_completed(tasks):
        result = future.result()
        print(result)

>>> 1
    2
    3
    4

```

### wait
> **concurrent.futures.wait(fs, timeout=None, return_when=ALL_COMPLETED)**

> **fs**: 执行的序列

> **timeout**: 等待的最大时间，如果超过这个时间即使线程未执行完成也将返回

> **return_when**: 表示wait返回结果的条件，默认为 ALL_COMPLETED 全部执行完成再返回

> * FIRST_COMPLETED
>
    函数将在任意可等待对象结束或取消时返回。

> * FIRST_EXCEPTION
>
    函数将在任意可等待对象因引发异常而结束时返回。
    当没有引发任何异常时它就相当于 ALL_COMPLETED。

> * ALL_COMPLETED 
>
    函数将在所有可等待对象结束或取消时返回。


```python
from concurrent.futures import ThreadPoolExecutor, wait
import time


def get_page(url):
    time.sleep(url)
    return url


with ThreadPoolExecutor(max_workers=5) as t:  # 创建一个最大容纳数量为5的线程池
    tasks = [t.submit(get_page, page) for page in range(1, 5)]

    a, b = wait(tasks)
    print(a)
    print(b)

>>> {<Future at 0x1c071fb1f28 state=finished returned int>, <Future at 0x1c071fb1d68 state=finished returned int>, <Future at 0x1c071f9fd68 state=finished returned int>, <Future at 0x1c071d78278 state=finished returned int>} 
    set()
```

### map
> **concurrent.futures.Executor.map(fn, *iterables, timeout=None)**

> **fn**: 第一个参数 fn 是需要线程执行的函数

> ***iterables**: 第二个参数接受一个可迭代对象

> **timeout**: 第三个参数 timeout 跟 wait() 的 timeout 一样，但由于 map 是返回线程执行的结果，如果 timeout小于线程执行时间会抛异常 TimeoutError

```python
from concurrent.futures import ThreadPoolExecutor
import time


def get_page(url):
    time.sleep(url)
    return url


URLS = [url for url in range(1, 4)]

with ThreadPoolExecutor(max_workers=5) as executor:  # 创建一个最大容纳数量为5的线程池
    for result in executor.map(get_page, URLS):
        print(result)

>>> 1
    2
    3
```

## 回调函数
回调函数(add_done_callback)是在调用线程完成后再调用的
```python
from concurrent.futures import ThreadPoolExecutor, wait
import threading
import time


def get_page(url):
    time.sleep(url)
    return url


def call_back(worker):
    print(f'tid: {threading.current_thread().ident}', worker.result())


with ThreadPoolExecutor() as t:
    tasks = []
    for page in range(1, 5):
        task = t.submit(get_page, url=page)
        task.add_done_callback(call_back)
        tasks.append(task)
    wait(tasks)

>>> tid: 6392  1
    tid: 14936 2
    tid: 12516 3
    tid: 10524 4
```

## 异常处理
* 通过添加回调函数的方法处理异常

```python
import logging


def executor_callback(worker):
    logging.info(f'finished')
    worker_exception = worker.exception()
    if worker_exception:
        logging.exception(worker_exception)
```

## 备注
* 一定使用with关键字处理线程池，在某些情况下线程池可能不能自动回收线程资源，with可以避免内存持续增长等情况
