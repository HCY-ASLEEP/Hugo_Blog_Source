---
title: Arch Linux  中文输入法
date: 2023-02-11 13:06:55
description: 使用于大部分桌面？
tags:
    - Arch
    - Linux
---

首先先是 locale 编码设置

1. locale配置
    
    - 可以执行命令locale查看当前配置
    
        ```bash
        $ locale
        LANG=en_US.UTF-8
        LANGUAGE=en_US
        LC_CTYPE="en_US.UTF-8"
        LC_NUMERIC=en_US.UTF-8
        LC_TIME=en_US.UTF-8
        LC_COLLATE="en_US.UTF-8"
        LC_MONETARY=en_US.UTF-8
        LC_MESSAGES="en_US.UTF-8"
        LC_PAPER=en_US.UTF-8
        LC_NAME=en_US.UTF-8
        LC_ADDRESS=en_US.UTF-8
        LC_TELEPHONE=en_US.UTF-8
        LC_MEASUREMENT=en_US.UTF-8
        LC_IDENTIFICATION=en_US.UTF-8
        LC_ALL=
        ```
    - 注意：设置LC_ALL变量，会覆盖除了LANGUAGE之外的所有locale环境变量，因此尽量不要使用它
    
2. 安装中文locale

    - 使用UTF-8的locale，将en_US.UTF-8和zh_CN.UTF-8的注释从配置文件/etc/locale.gen去掉，即删除行首的#
    
        ```bash
        # /etc/locale.gen
        _US.UTF-8 UTF-8
        _CN.UTF-8 UTF-8
        ```
        
    - 然后执行
    
        ```bash
        sudo locale-gen
        ```

3. 配置LANG和LANGUAGE

    - 设置locale全局配置文件/etc/locale.conf ，但不推荐在该文件中配置全局的中文locale，会导致 tty 乱码
    
        ```bash
        # /etc/locale.conf
        LANG=en_US.UTF-8
        ```
        
    - 不同的用户可以在下列文件中，设置各自的环境变量
    
        ```bash
        # ~/.bashrc：每次使用终端登录时读取并运用里面的设置
        # ~/.xinitrc：每次使用 startx 或 SLiM 启动 X 界面时读取并运用里面的设置
        # ~/.xprofile：每次使用 GDM 等显示管理器登录时读取并运用里面的设置
        
        export LANG=zh_CN.UTF-8
        export LANGUAGE=zh_CN:en_US
        ```
        
4. 安装中文字体

    ```bash
    # 这些是官方仓库里面的文泉驿字体
    hcy@archlinux:~$ sudo pacman -Ss wqy
    community/wqy-bitmapfont 1.0.0RC1-5 [已安装]
        A bitmapped Song Ti (serif) Chinese font
    community/wqy-microhei 0.2.0_beta-11 [已安装]
        A Sans-Serif style high quality CJK outline font
    community/wqy-microhei-lite 0.2.0_beta-10 [已安装]
        The "Light" face of WenQuanYi Micro Hei font family
    community/wqy-zenhei 0.9.45-9 [已安装]
        A Hei Ti Style (sans-serif) Chinese Outline Font.
    ```

安装 fcitx5 实现输入中文具体步骤如下

```bash
sudo pacman -S fcitx5-im 
sudo pacman -S fcitx5-chinese-addons
```

其中 fcitx5-chinese-addons 包含了大量中文输入方式：拼音、双拼、五笔拼音、自然码、仓颉、冰蟾全息、二笔等

然后在环境变量配置文件 /etc/environment 中添加如下内容


```bash
# /etc/environment
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx
```

然后配置自动启动，提供以下3种方法，第一个不行，试第2个，然后第3个

1. 如果支持XDG桌面环境，例如KDE,GNOME, Xfce,默认重启后即可

2. 在~/.config/autostart目录下添加fcitx-autostart.desktop文件，可以从目录etc/xdg/autostart中复制

3. 对于i3-wm,可以在配置文件~/.config/i3/config中添加如下代码
    
    ```bash
    # fcitx5
    exec_always --no-startup-id fcitx5
    ```
    


