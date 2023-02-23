---
title: Arch Linux live 安装使用 WIFI
date: 2023-02-11 13:17:48
description: 命令行启动 WIFI
tags:
    - Arch
    - Linux
---

- 先用命令 ip link查看网络接口

    ```bash
    ip link
    ```
    
- 查看到我的机器的无线网络接口是wlan0(不同的机器可能名字不同)，这里 wlan0 为例

- 默认是关闭的状态，需要先开启它，而开启它之前还需要先激活它(即取消禁用，我这台机器默认是blockeded，禁用的)

    ```bash
    rfkill unblock wifi    # 取消禁用wifi设备
    ip link set wlan0 up   # 开启wlan0
    ```

- 输入iwctl进入交互式提示符（interactive prompt），配置并连接到互联网

    ```
    station wlan0 scan
    station wlan0 get-networks
    station wlan0 connect <network name>
    station wlan0 show
    exit　　# 回到命令行
    ```
    
- ping百度可以ping通，就说明已经连接上了互联网


