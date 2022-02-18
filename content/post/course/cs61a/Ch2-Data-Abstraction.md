---
title: "计算机导论 数据抽象"
date: 2022-02-05T13:35:54+08:00
lastmod: 2022-02-05T13:35:54+08:00
description: ""
draft: false
tags: ["计算机导论", "计算机课程"]
categories: ["计算机课程"]
---

# 数据抽象

## 数据抽象的必要性

程序中的许多数据都是 **复合（`compound`）** 值 ，例如：

* 日期：由年月日组成
* 经纬度：由经度和纬度组成

数据抽象可以让我们将复合的值作为一个整体操作，不需要关注复合值的具体组成细节；

数据抽象使程序更容易设计、维护和修改。

> 将程序中处理数据表示方式的部分与处理数据处理方式的部分隔离开来的通用技术是一种强大的设计方法，称为 **数据抽象**（`data abstraction`） 。

数据抽象在性质上与功能抽象相似。当我们创建一个函数抽象时，一个函数如何实现的细节可以被隐藏，特定函数本身可以被具有相同整体行为的任何其他函数替换。

换句话说，我们可以做一个抽象，将函数的使用方式与函数实现的细节分开。类似地，数据抽象也可以将复合数据值的使用方式与其构建方式的细节隔离开来。

## 数据抽象的实例

* `rational(n, d)` 返回具有分子`n`和分母`d`的有理数

  ```python
  from fractions import gcd
  
  def rational(n, d):
      g = gcd(n, d)
      return (n//g, d//g)
  ```
  
* `numer(x)` 返回有理数`x`的分子

  ```python
  def numer(x):
      return x[0]
  ```

* `denom(x)` 返回有理数`x`的分母

  ```python
  def denom(x):
      return x[1]
  ```

* `add_rationals(x, y)` 实现两个有理数间的加法操作

  ```python
  def add_rationals(x, y):
      nx, dx = numer(x), denom(x)
      ny, dy = numer(y), denom(y)
      return rational(nx * dy + ny * dx, dx * dy)
  ```

* `mul_rationals(x, y)` 实现两个有理数间的乘法操作

  ```python
  def mul_rationals(x, y):
      return rational(numer(x) * numer(y), denom(x) * denom(y))
  ```

* `print_rationals(x)` 打印有理数

  ```python
  def print_rationals(x):
      print(numer(x), '/', denom(x))
  ```

* `rationals_are_equal(x, y)` 判断两个有理数是否相等

  ```python
  def rationals_are_equal(x, y):
      return numer(x) * denom(y) == numer(y) * denom(x)
  ```

  

## 数据抽象的屏障

|                 抽象层                 |                            描述                            |                             示例                             |
| :------------------------------------: | :--------------------------------------------------------: | :----------------------------------------------------------: |
| 原始表示（`Primitive Representation`） | 使用语言内建的数据结构和选择符为数据抽象实现选择器和构造器 |                        [n, d] , x[0]                         |
|     数据抽象（`Data Abstraction`）     |                  数据抽象的选择器和构造器                  |                    rational, numer, denom                    |
|       用户程序（`User Program`）       |                   用户计算程序的实现函数                   | add_rational, mul_rational, rationals_are_equal, print_rational |

这些函数由更高抽象级别调用较低抽象级别实现。

当程序的某个部分可以使用较高层次的函数而转而使用较低层次的函数时，就会发生 **抽象屏障违规（`abstraction barrier violation`）**。

## 树的抽象实现

### 树的描述

#### 树的递归描述

* 一棵树有一个 **根标签（`root label`）** 和 **分支（`branch`）** 的列表
* 每个分支自身也是一棵树
* 一棵没有分支的树被称为 **叶子（`leaf`）**
* 一棵树从根开始

#### 树的相对描述

* 树中的每个元素称为一个 **节点（`node`）**
* 每个节点都有一个 **标签（`label`）** ，标签可以是任何值
* 一个节点可以是另一个节点的父节点/孩子节点
* 顶部节点是**根节点(`root node`)**

### 树的实现

```python
def tree(root_label, branches=[]):
    for branch in branches:
        assert is_tree(branch), 'branches must be trees'
    return [root_label] + list(branches)

def label(tree):
    return tree[0]

def branches(tree):
    return tree[1:]

def is_tree(tree):
    if type(tree) != list or len(tree) < 1:
        return False
    for branch in branches(tree):
        if not is_tree(branch):
            return False
    return True

def is_leaf(tree):
    return not branches(tree)
```

### 树的应用

#### 斐波那契树

```python
def fib_tree(n):
    if n <= 1:
        return tree(n)
    
    else:
        left, right = fib_tree(n - 2), fib_tree(n - 1)
        fib_n = label(left) + label(right)
        return tree(fib_n, [left, right])
```

#### 分区树

```python
def partition_tree(n, m):
    if n == 0:
        return True
    elif n < 0 or m = 0:
        return tree(False)
    else:
        left = partition_tree(n - m, m)
        right = partition_tree(n, m-1)
        return tree(m, [left, right])
```

## 可变性

### 对象

> **对象（`object`）** 是一组数据和 **行为（`behavior`）** 的集合，
>
> 一种类型的对象称为 **类（`class`）**。

Python 中的每个值都是一个对象

- 所有对象都有属性
- 对象通常有关联的方法

