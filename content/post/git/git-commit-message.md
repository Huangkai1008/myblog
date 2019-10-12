---
title: "Git Commit Message编写规范"
date: 2019-07-12T14:14:15+08:00
lastmod: 2019-10-10T14:14:15+08:00
description: ""
draft: false
tags: ["git", "tips"]
categories: ["git"]
---

Git提交代码时需要提交Message, 为了使得提交信息更清晰明了, 需要确定规范

现在比较流行的规范是Angular规范, 也根据此规范衍生了Conventional Commits specification

## 规范
### **格式**
  ```
   <type>(<scope>): <subject>
   <BLANK LINE>
   <body>
   <BLANK LINE>
   <footer>
  ```
  按照空行分割为三个部分, 分别为**Header**，**Body** 和 **Footer**
  其中，Header 是必需的，Body 和 Footer 可以省略
  不管是哪一个部分，任何一行都不得超过72个字符（或100个字符）, 这是为了避免自动换行影响美观

### **组成**
####  **Header**
Header部分只有一行，包括三个字段：type（必需）、scope（可选）和subject（必需）
    
1. type

    ​type用于说明 commit 的类别，只允许使用下面7个标识
    		                                                                                                                                                                                                                                   
    > 1. feat：新功能（feature）
    > 2. fix：修补bug
    > 3. docs：文档（documentation）
    > 4. style： 格式（不影响代码运行的变动）
    > 5. refactor：重构（即不是新增功能，也不是修改bug的代码变动）
    > 6. test：增加测试
    > 7. chore：构建过程或辅助工具的变动
    
    _如果type为feat和fix，则该 commit 将肯定出现在 Change log 之中。其他情况（docs、chore、style、refactor、test）由你决定，要不要放入 Change log，建议是不要_
            
            
                                                                                                                                                                                                                                                                                                                                 
        
2. scope

    scope用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同    
               
3. subject
        
    subject是 commit 目的的简短描述，不超过50个字符
            
    > - 以动词开头，使用第一人称现在时，比如change，而不是changed或changes
    > - 第一个字母小写
    > - 结尾不加句号  

####  **Body**
Body 部分是对本次 commit 的详细描述，可以分成多行

> 使用第一人称现在时，比如使用change而不是changed或changes

> 应该说明代码变动的动机，以及与以前行为的对比

#### **Footer**
Footer部分可以用于表达**不兼容变动**和**关闭Issue**

* 不兼容变动

    如果当前代码与上一个版本不兼容，则 Footer 部分以BREAKING CHANGE开头，后面是对变动的描述、以及变动理由和迁移方法

* 关闭Issue
    
    > Closes APS-151





                                                                                                                                                             
## Jetbrains工具配置
* **git commit template**   

    >提交信息模板
* **Gitmoji**
               
  
    >添加emoji表情在commit信息中



