---
title: "计算机导论 函数抽象"
date: 2022-02-05T13:35:54+08:00
lastmod: 2022-03-05T08:58:54+08:00
description: ""
draft: false
tags: ["计算机导论", "计算机课程"]
categories: ["计算机课程"]
---

# 函数抽象

## 编程的要素

每个具有描述力的强大的编程语言一般都有这三种机制：

* **原始的表达式（`expression`）和语句（`statement`）**
* **组合（`combination`）的方式**
* **抽象（`abstraction`）的手段**

### 表达式

> **表达式** 是用于描述计算过程并得出值的公式。

#### 表达式的定义

* 表达式可以通过 **操作符（`operator`）** 定义：

    ```python
    18 + 69

    2 ** 100
    ```

* 也可以通过 **函数调用(`function call`)** 的方式：

    ```python
    pow(2, 100)
    
    max(50, 300)
    ```

#### 表达式的调用

```python
   add     (   18     ,   69    )
----------  ---------  ---------
 Operator    Operand    Operand
```

1. 评估操作符
2. 评估操作数
3. 将运算符（函数）应用于计算后的操作数（参数）

操作符和操作数都属于表达式，所以都需要先计算出值。

### 程序（`program`)

* 程序通过操作值工作
* 程序中的表达式求值
  * 表达式: `'a' + 'hoy'`
  * 值：`ahoy`

### 值（`value`）

> 程序操作值，每个值都有自己确定的**类型（`data type`）**。

### 命名（`name`）

名称可以绑定（`bind`）到值，一种绑定的方法是使用 **赋值语句（`assignment statement`）**

```python
    x     =     7
 -------     -------
   Name       Value
```

值可以是任意的表达式。

#### 命名规范

名称应该传达它们所绑定的值的含义或目的。函数名称通常传达它们的效果、行为或者返回值。

如果名称代表通用量，则名称可以很短：计数、任意函数、数学运算的参数等。

* `n`, `k`, `i`- 通常是整数
* `x`, `y`, `z`- 通常是实数或坐标
* `f`, `g`, `h`- 通常是函数

## 函数

> 函数是执行特定任务并且可以轻松复用的代码段，
> 函数使用 **参数(`argument`)** 作为输入，将 **返回值（`return value`）** 作为输出，
> 函数提供了编程语言功能抽象的实现。

### 纯函数和副作用

#### 副作用

> **副作用(`side effect`)** 是指调用一个函数时，除了返回一个值之外，还做了其他一些额外的工作。

比较常见的副作用有打印到控制台：

```python
print(-1)
```

#### 纯函数

**纯函数（`pure function`）** 只返回值，不含有任何副作用：

```python
def add(x, y):
    return x + y
```

**非纯函数（`non-pure function`）** 含有副作用：

```python
def add(x, y):
    print(x, y)
    return x + y
```

### 函数的描述

```python
def square(x):
    """Returns the square of X."""
    return x * x
```

对于以上的函数有：

|        概念        |                    描述                    |           示例           |
| :----------------: | :----------------------------------------: | :----------------------: |
|  定义域(`domain`)  |        可能作为参数的所有输入的集合        |      `x`是一个数字       |
|  值域（`range`）   |        可能返回的值的所有输出的集合        | `square`返回一个非负实数 |
| 表现（`behavior`） | 纯函数的表现是它在输入和输出之间建立的关系 |  `square`返回`x`的平方   |

### 函数的抽象

* 按照 **参数化（`parameterization`）** 抽象
* 按照 **规范（`specification`）** 抽象

### 函数的设计

* 每个函数只做一个工作，这个工作可以用一个简短的名称来识别，并且可以在一行文本中进行表征
* 拒绝重复定义（Don't repect yourself, DRY）
* 函数需要通用定义，函数的设计需要适应同一功能下可能发生的各种情况

#### 函数定义文档

函数的定义通常包括描述函数的文档，此文档一般被称为 **docstring**。

以下的函数示例可以作为参考：

```python
def pressure(v, t, n):
    """Compute the pressure in pascals of an ideal gas.
    
    Applies the ideal gas law: http://en.wikipedia.org/wiki/Ideal_gas_law
    
    Args:
        v: volume of gas, in cubic meters
        t: absolute temperature in degrees kelvin
        n: particles of gas

    """
    k = 1.38e-23  # Boltzmann's constant
    return n * k * t / v
```

可以通过`help(pressure)`查看此文档。

更多参考信息可以查看如下链接：

[docstring guidelines](https://www.python.org/dev/peps/pep-0257/)

### 高阶函数

> **高阶函数（`higher-order function`）** 的特征是：
>
> * 接受另一个函数作为参数
> * 将函数作为返回值
>
> 除此之外的函数都被称为 **一阶函数（`first-order function`）**

#### 柯里化

> 用高阶函数将一个接受多个参数的函数转换为一个函数链，每个函数接受一个参数。更具体地说，给定一个函数`f(x, y)`，我们可以定义一个函数`g`使得`g(x)(y)`等价于`f(x, y)`。这里，`g`是一个高阶函数，它接受单个参数`x`并返回另一个接受单个参数`y`的函数。这种转换称为**柯里化（`curry`）**。

以下为一个柯里化的示例：

```python
def curried_pow(x):
    def h(y):
        return pow(x, y)
    return h

>> curried_pow(2)(3)
8
```

### 递归函数

> 如果函数体直接或间接调用函数本身，则函数称为 **递归（`recursive`）** 函数。

#### 递归函数的通用模式

* **基线条件（`base case`）** ：在没有递归调用的情况下进行计算（最小的子问题）
* **递归条件（`recusive case`）**  : 通过递归调用进行计算（进一步分解问题）
* 判断是否是符合基线条件的 **条件语句（`conditional statement`）**

```python
def sum_digits(n):
    if n < 10: # BASE CASE
        return n
    else:         # RECURSIVE CASE
        all_but_last = n // 10
        last = n % 10
    return sum_digits(all_but_last) + last
```

#### 相互递归

> 当一个递归过程被划分为两个相互调用的函数时，这些函数被称为 **相互递归（`mutually recursive`）**。

```python
def is_even(n):
    if n == 0:
        return True
    else:
        return is_odd(n - 1)

def is_odd(n):
    if n == 0:
        return False
    else:
        return is_even(n - 1)
```

#### 树递归

> **树递归（`tree recursion`）** 中一个函数会多次调用自身。

```python
def count_partitions(n, m):
    """Count the ways to partition n using parts up to m."""
    if n == 0:
        return 1
    elif n < 0:
        return 0
    elif m == 0:
        return 0
    else:
        return count_partitions(n - m, m) + count_partitions(n, m - 1)
```
