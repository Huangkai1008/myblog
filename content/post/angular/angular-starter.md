---
title: "Angular Starter"
date: 2018-10-09T13:56:20+08:00
lastmod: 2018-10-09T13:56:20+08:00
description: ""
draft: false
tags: ["angular", "frontend", "typescript"]
categories: ["angular", "frontend", "typescript"]
---
## 安装Angular
### 确保node/npm已安装
```bash
node -v 查看node版本
npm -v 查看npm版本
```

### 安装typescript
```bash
npm install -g typescript
```

### 安装Angular CLI
```bash
npm install -g @angular/cli 
ng version  # 验证angular-cli版本
```


## 建立一个新的Angular项目
Angular CLI 为我们提供了两种方式，用于创建新的应用程序：

-  `ng init` - 在当前目录创建新的应用程序

-  `ng new` - 创建新的目录，然后在新建的目录中运行 ng init 命令

     因此 `ng new` 与 `ng init` 的功能是相似的，只是 `ng new` 会为我们创建新的目录

### 创建应用

```bash
ng new my-app
```

### 可用选项

- `--dry-run`: boolean, 默认为 `false`, 若设置 `dry-run` 则不会创建任何文件
- `--verbose`: boolean, 默认为 `false`
- `--link-cli`: boolean, 默认为 `false`, 自动链接到 `@angular/cli` 包
- `--skip-install`: boolean, 默认为 `false`, 表示跳过 `npm install`
- `--skip-git`: boolean, 默认为 `false`, 表示该目录不初始化为 git 仓库
- `--skip-tests`: boolean, 默认为 `false`, 表示不创建 tests 相关文件
- `--skip-commit`: boolean, 默认为 `false`, 表示不进行初始提交
- `--directory`: string, 用于设置创建的目录名，默认与应用程序的同名
- `--source-dir`: string, 默认为 `'src'`, 用于设置源文件目录的名称
- `--style`: string, 默认为 `'css'`, 用于设置选用的样式语法 (`'css'`, `'less'` or `'scss'`)
- `--prefix`: string, 默认为 `'app'`, 用于设置创建新组件时，组件选择器使用的前缀
- `--mobile`: boolean, 默认为 `false`,表示是否生成 Progressive Web App 应用程序
- `--routing`: boolean, 默认为 `false`, 表示新增带有路由信息的模块，并添加到根模块中
- `--inline-style`: boolean, 默认为 `false`, 表示当创建新的应用程序时，使用内联样式
- `--inline-template`: boolean, 默认为 `false`, 表示当创建新的应用程序时，使用内联模板