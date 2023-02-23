---
title: Gnome 下设置默认 terminal
date: 2023-02-20 13:42:53
description: Gnome default terminal
tags:
    - Gnome
    - Linux
---

这个问题困扰了我好久，我已经尝试如下方案：

1. gsettings
2. mimefile
3. gnome-control-center
4. dconf
    
这些都没有用。。。

</br>

直到我看见了 reddit 的一个讨论，问题解决了，我想使用的默认终端是 alacritty：

```bash
cd /usr/bin && sudo ln -s alacritty xterm
```

[关于此问题的 reddit 传送门](https://www.reddit.com/r/Alacritty/comments/hecdqv/changing_default_terminal_to_alacritty_in_gnome/)

