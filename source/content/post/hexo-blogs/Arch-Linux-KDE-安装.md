---
title: Arch Linux KDE 安装
date: 2023-02-21 10:45:33
description: Arch KDE Install
tags:
    - Arch 
    - KDE
---

1. 若有n卡驱动

    ```bash
    sudo pacman -S nvidia
    ```
    
2. 安装 plasma

    ```bash
    sudo pacman -S plasma   #完整版
    plasma-meta             #精简版
    plasma-desktop          #极简版，如果是这个版本，记得安装 alacritty 或者 konsole ，并不自带终端
    ```
    
3. 安装启动界面

    ```bash
    sudo pacman -S sddm         #启动界面
    sudo systemctl enable sddm  # 启用启动界面
    ```
    
