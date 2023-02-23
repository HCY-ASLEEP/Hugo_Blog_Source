---
title: Arch KDE Fcitx5 Alacritty 显示问题
date: 2023-02-21 05:38:27
description: Arch Fcitx5 中文输入法 alacritty wakeup 显示问题
tags:
    - Arch
    - KDE
    - Linux
---


安装完成 fcitx5 中文输入法之后，其他界面都可以正常使用，就是 alacritty 不能使用，就连激活都没有办法激活

**[直到我看到了这一篇博客](https://www.csslayer.info/wordpress/linux/use-plasma-5-24-to-type-in-alacritty-or-any-other-text-input-v3-client-with-fcitx-5-on-wayland/)**

只需要在 KDE Config 里面开启 Virtual Keyboard 就行了

![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/arch-kde-fcitx5-image-1.png)

然后就可以愉快地享受 alacritty 啦

![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/arch-kde-fcitx5-image-2.png)
