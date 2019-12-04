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
          PrimaryKeyConstraint('pk1', 'pk2'),
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

* subquery使用实例
  ```python
  conditions = list()
  for key, value in material_period.items():
        condition = and_(
            CraftEntityAttrs.attr_number == key, CraftEntityAttrs.attr_value == value
        )
        conditions.append(condition)

    if not conditions:
        return list()

    stmt = (
        db.session.query(CraftEntityAttrs.entity_id, CraftEntityAttrs.cat_number)
        .filter(or_(*conditions))
        .subquery()
    )

    query = db.session.query(
        CraftEntityPeriodHours.proc_number,
        CraftEntityPeriodHours.period,
        CraftEntityPeriodHours.hours,
        CraftEntityPeriodHours.major_wrapper_skill_level,
        stmt.c.cat_number,
    ).filter(CraftEntityPeriodHours.entity_id == stmt.c.entity_id)
    ```
    ```python
    stmt = (
        db.session.query(ProducePlan.row_id, ProducePlan.row_seq)
        .filter(ProducePlan.proc_number.in_(constants.COIL_PROC_NUMBERS))
        .distinct()
        .subquery()
    )

    query = (
        BatchDetail.query.join(
            stmt,
            and_(
                BatchDetail.row_id == stmt.c.row_id,
                BatchDetail.row_seq == stmt.c.row_seq,
            ),
        )
        .join(PlanRow, BatchDetail.row_id == PlanRow.id)
        .join(RowProject, PlanRow.project_id == RowProject.id)
        .join(Order, RowProject.order_id == Order.id)
        .with_entities(
            Order.order_number,
            Order.id.label('order_id'),
            Order.project_name,
            RowProject.row_project_number,
            RowProject.id.label('project_id'),
            Order.purchase_unit,
            RowProject.fac_number,
            RowProject.mat_number,
            RowProject.mat_desc,
            PlanRow.com_qty,
            BatchDetail.row_id,
            PlanRow.plan_row_number,
            BatchDetail.batch_id,
            BatchDetail.batch_number,
            BatchDetail.batch_qty,
            BatchDetail.batch_seq,
            BatchDetail.single_pack_cycle,
        )
        .order_by(RowProject.id, BatchDetail.batch_id, BatchDetail.batch_seq)
    )
    ```
    ```python
    stmt = (
        db.session.query(ProducePlan.project_id)
        .outerjoin(
            ProducePlanCompletion, ProducePlan.plan_id == ProducePlanCompletion.plan_id
        )
        .filter(
            or_(
                ProducePlanCompletion.completion.is_(None),
                ProducePlanCompletion.completion
                == constants.ProducePlanCompletion.not_scheduled.value,
            )
        )
        .distinct()
        .subquery()
    )

    query = db.session.query(ProducePlan.project_id).filter(
        ProduceUserPlan.project_id.in_(stmt), ProduceUserPlan.proc_type == 'design'
    )
    ```