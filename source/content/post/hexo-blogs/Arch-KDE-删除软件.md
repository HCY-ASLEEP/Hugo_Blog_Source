---
title: Arch KDE 删除软件
date: 2023-02-21 05:20:57
description: Arch KDE Removal
tags:
    - Arch
    - Linux
    - KDE
---

我使用的是 archinstall 来安装 Arch KDE 的，在安装的时候是以 plasma-meta 整体安装，所以想卸载 discover 等组件的时候就会破坏依赖，所以采取下面的方法来卸载

```bash
sudo pacman -Rdd discover                           # Software market
sudo pacman -Rdd plasma-welcome
sudo pacman -Rdd plasma-systemmonitor
sudo pacman -Rdd drkonqi                            # Crash reporter
```

这种卸载方式只是单独卸载目标软件包，虽然在下次升级 plasma-meta 的时候还是会安装回来的，不过对我问题不大，升级之后再卸载就行

如果一个包可以安全被卸载，想卸载干净的话 (卸载只是被它依赖的依赖)

```bash
sudo pacman -Rsn <target>
```
