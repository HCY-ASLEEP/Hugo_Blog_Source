---
title: "Alacritty 配置 JetBrains Mono 字体"
date: 2023-12-26T14:37:39Z
draft: false
---

在 ~/.config/alacritty/ 目录下创建 alacritty.yml
```bash
mkdir ~/.config/alacritty/;\
touch ~/.config/alacritty/alacritty.yml
```
写入 alacritty.yml 配置
```yml
font:
  normal:
    family: Jetbrains Mono
    style: Regular
  size: 12
```
