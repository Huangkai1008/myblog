---
title: "Manjaro安装配置"
date: 2019-08-01T22:21:57+08:00
lastmod: 2019-12-02T11:02:57+08:00
description: ""
draft: true
tags: ["linux", "install", "manjaro"]
categories: ["linux"]
---
## 基本安装
1. 使用`rufus`以dd模式写入U盘
2. 从u盘启动
3. 将manjaro启动项中的`driver`和`boot`添加或修改`driver=intel`才能进入安装界面(双显卡笔记本)
4. 安装系统, 注意不要联网, 否则容易卡在安装
5. reboot进入系统
6. manjaro启动项中quiet后增加`nouveau.modeset=0`(双显卡)


## 双显卡使用prime管理连接外接显示器
1. 删除bumblebee或者开源驱动
   ```bash
   sudo mhwd -r pci nonfree 0300
   ```

2. 安装nvidia私有闭源驱动
   * 方法一: 
        ```bash
        sudo mhwd -i pci video-nvidia  
        ```
        或
        
        ```bash
        sudo mhwd -i pci video-nvidia-390xx  # 390xx或者435xx, 数字是驱动版本...
        ```
   
   * 方法二
     `系统设置-硬件设定`中右键安装`video-nvidia-390xx`之类的驱动

3. 安装依赖
    ```bash
    sudo pacman -S linuxXXX-headers acpi_call-dkms xorg-xrandr xf86-video-intel git 
    ```
    > 注: XXX 为内核版本， 本来我的5.3有点问题，降级成4.19才可以，以4.19为例便是
    > `linux419-headers`                                                                                                                 

4. 注入
    ```bash
    sudo modprobe acpi_call
    ```

5. 使用github上的脚本
    ```bash
    cd ~  # 建议在用户目录下操作
    git clone https://github.com/dglt1/optimus-switch-sddm.git
    cd optimus-switch-sddm
    chmod +x install.sh
    sudo ./install.sh
    ```
6. reboot