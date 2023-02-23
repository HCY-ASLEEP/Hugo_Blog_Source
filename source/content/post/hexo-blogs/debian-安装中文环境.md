---
title: Debian 安装中文环境
date: 2023.1.3 9:27:15
description: 容器里面可能要先安装 locale
tags:
    - Linux 
---
- 先查看是否有中文语言环境

    ```bash
    locale -a    
    ```
    
- 安装语言环境（root权限）记得选 zh_CN-utf8
    
    ```
    dpkg-reconfigure locales
    ```

- 安装中文字体

    ```bash
    apt-get install *wqy*
    ```
    
    

