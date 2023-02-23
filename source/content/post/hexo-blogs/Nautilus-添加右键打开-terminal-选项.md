---
title: Nautilus 添加右键打开 terminal 选项
date: 2023-02-21 04:50:37
description: Nautilus open any terminal
tags:
    - Gnome
    - Linux
    - Arch
---

在换到 KDE 之后，我还是怀念 Nautilus ，而非 Dolphin ，因此我决定继续在 KDE 上使用 Nautilus 。而设置 Nautilus 右键打开的 terminal 选项，指定这个打开的选项为我的默认终端 alacritty ，需要 Nautilus 拓展来做到

</br>

**["这个是拓展的 Github 地址"](https://github.com/Stunkymonkey/nautilus-open-any-terminal)**

</br>

我使用的是 Arch ，从 aur 下载安装

```bash
yay -S nautilus-open-any-terminal
```

重启 Nautilus

```bash
nautilus -q
```

在命令行设置 alacrity 为指定终端

```bash
gsettings set com.github.stunkymonkey.nautilus-open-any-terminal terminal alacritty
```
