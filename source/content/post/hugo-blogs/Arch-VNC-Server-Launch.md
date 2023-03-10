---
title: "Arch 启用 VNC Server"
date: 2023-03-10T22:09:05+08:00
tags:
    - Arch
    - Linux
---

在本文写的时候，VNC 远程连接 Wayland 显示协议只能说非常不成熟，甚至有点离谱，键盘压根输入不了，颜色反转，声音不能正常 pipewire 远程，因此如果将自己的桌面环境作为 VNC 远程后端，还是 X11/Xorg 协议体验好很多。与此同时，桌面环境里面，Xfce4 兼顾了流畅性与桌面功能健全性，虽然说 KDE 也是很健全的，但是 KDE 的动画在 VNC 远程里面反而是消耗资源的问题所在，远程体验不流畅。

1. 使用一个 GUI 的 VNC Server Manager：
    
    ```bash
    # KDE Remote Frame Buffer ，但是也是适用与其他桌面环境如 Xfce4 的
    sudo pacman -S krfb
    ```

2. 我是先安装了 KDE 和 SDDM 显示管理器再安装的 Xfce4

    ```bash
    sudo pacman -S xfce4
    ```
    安装完 Xfce4 之后在 SDDM 的左下角就可以选择 Xfce4 还是 KDE 桌面
    
3. krfb 配置

    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-35.png)
    
    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-37.png)
    
    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-38.png)
    
    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-38_1.png)
    
    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-39.png)
    
    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-39_1.png)
    

4. VNC 客户端推荐：

    - Windows TigerVNC
    - Linux krdc(KDE Remote Desktop Client)
    - Android bVNC
    
5. 有一些包是其他教程里面推荐的，不过我觉得应该不用

    ```bash
    sudo pacman -S xfce4-goodies   # Xfce4 包组（group）
    sudo pacman -S xorg xorg-xinit
    sudo pacman -S xdg-desktop-portal
    ```
