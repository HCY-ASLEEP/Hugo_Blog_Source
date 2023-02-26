---
title: "Thunar 设置打开终端"
date: 2023-02-26T19:27:50+08:00
tags:
    - Linux
    - XFCE
    - KDE
    - GNOME
description: XFCE 文件浏览器打开默认终端
---

👉 [感谢 askubuntu](https://askubuntu.com/questions/891680/xubuntu-thunar-open-terminal-here-opens-konsole-in-homefolder)

> In Thunar Edit > Configure custom actions... then edit "Open Terminal Here", and replace `exo-open --working-directory %f --launch TerminalEmulator` with `konsole --workdir %f`

经过我的尝试，直接替换成 alacritty 也是可以的
