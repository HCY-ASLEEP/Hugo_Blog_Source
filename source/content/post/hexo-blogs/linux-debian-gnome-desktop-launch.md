---
title: Gnome Desktop File 参考 
date: 2022-10-23 13:25:51
tags:
    - Gnome
    - Linux
---

eg:<!--more-->

```bash
[Desktop Entry]
Encoding=UTF-8
Name=Tabby
Comment=Tabby
Exec=/home/asleep/softwares/tabby/tabby/tabby %U
Icon=/home/asleep/softwares/tabby/tabby/tabby.svg
Terminal=false
StartupNotify=true
Type=Application
Categories=Application;Development;
StartupWMClass=tabby
```

需要改变的是 name，common，exec，icon，startupwmclass

.desktop 文件位于 /usr/share/application/ 下面

StartupWMClass

```bash
xprop | grep CLASS
```



