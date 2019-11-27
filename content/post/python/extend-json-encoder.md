---
title: "扩展Python Json Encoder"
date: 2018-04-09T22:49:14+08:00
lastmod: 2018-04-09T22:49:14+08:00
description: ""
draft: false
tags: ["python", "json"]
categories: ["python"]
---



Python默认的json模块序列化并不是很全面，只能序列化基本的数据类型, 像一些时间格式或者自定义类型都不能序列化，所以在有些时候需要扩展json模块的json encoder

## 扩展

```python
import datetime as dt
import decimal
import json
import enum
from collections.abc import Iterator

class ExtendedEncoder(json.JSONEncoder):
    def default(self, o):
        if isinstance(o, dt.datetime):
            return o.strftime('%Y-%m-%d %H:%M:%S')
        elif isinstance(o, dt.date):
            return o.strftime('%Y-%m-%d')
        elif isinstance(o, decimal.Decimal):
            return float(o)
        elif isinstance(o, Iterator):
            return list(o)
        elif isinstance(o, enum.Enum):
            return o.value
        return json.JSONEncoder.default(self, o)
```



## 使用场景

* **日常格式化**

    例如对于日期格式的格式化

    ```python
    import datetime as dt
  
    now = dt.datetime.now()
    ```

    对于now如果使用json.dumps(t_now)便会触发<pre>TypeError: Object of type datetime is not JSON serializable</pre>
    使用扩展的Encoder

    ```python
     >>> json.dumps(now, cls=ExtendedEncoder)
     '2018-04-09 23:04:49'
    ```

* **Flask**

    修改flask类的json_encoder

    ```python
    from flask import Flask as _Flask
    class QuizFlask(_Flask):
      """
      自定义flask
      """
  
      json_encoder = ExtendedEncoder
  
      def make_response(self, rv):
          if rv is None:
              rv = dict()
  
          if isinstance(rv, Iterator):
              rv = list(rv)
  
          return super(QuizFlask, self).make_response(rv)
    ```

* **Tortoise-orm**

    模型jsonfield的encoder

  ```python
  import json
  
  from tortoise import fields
  from tortoise.models import Model
  
  __all__ = ['OurModel']
  
  class OurModel(Model):
      """示例model"""
  
      id = fields.IntField(pk=True)
      cat_ids = fields.JSONField(
          encoder=ExtendedEncoder, decoder=json.decoder
      ) 	# JsonField的encoder
  
  ```

  