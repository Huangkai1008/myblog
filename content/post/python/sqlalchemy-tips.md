---
title: "Sqlalchemy使用注意"
date: 2017-06-14T13:56:20+08:00
lastmod: 2019-09-25T13:56:20+08:00
description: ""
draft: false
tags: ["python", "mysql", "sqlalchemy", "tips"]
categories: ["python", "mysql", "sqlalchemy"]
---

* 遇到in查询之类的批量删除或者更新，可以使用**synchronize_session=False**

  ```python
  db.session.delete(synchronize_session=False)
  ```

* 使用**find_in_set**

  ```python
  from sqlalchemy.sql.expression import func
  
  db.session.query(Post).filter(func.find_in_set('10', Post.c.tag_id))
  ```

* 批量增加删除

  ```bash
  db.session.add_all(instances)
  db.session.delete_all(instances)
  ```

* Mysql IS NULL判断

  ```python
  isnot()
  is_()
  ```

* Mysql 联合主键

  ```python
  from sqlalchemy import PrimaryKeyConstraint
  
  class Node(Model):
      __table_args__ = (
          PrimaryKeyConstraint('pk1', 'pk2),
      )
  
  ```

* Flask_sqlalchemy支持Double精度类型字段

  ```python
  from sqlalchemy import Column
  from sqlalchemy.dialects.mysql import DOUBLE
  
  from app import db 
  
  class BaseModel(Model):
       id = db.Column(db.Integer, primary_key=True)         # Flask_sqlalchemy
       double_column = Column(DOUBLE, comment='双精度字段')  # Sqlalchemy mysql double column
  ```
