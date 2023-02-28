---
title: "KDE Slow Shutdown"
date: 2023-02-28T21:58:38+08:00
description: KDE 关机慢
tags:
    - Linux
    - KDE
    - Arch
---
Arch Plasma 界面关闭有时候要三分钟左右，查阅了网上大量资料，最终找到一个可以用的方法

- 编辑 systemd 配置

    ```bash
    sudo nvim /etc/systemd/system.conf
    ```
    
- 改里面的两项，先去掉注释，再改时间

    ```bash
    DefaultTimeoutStopSec=1ms
    DefaultRestartSec=1ms
    ```
