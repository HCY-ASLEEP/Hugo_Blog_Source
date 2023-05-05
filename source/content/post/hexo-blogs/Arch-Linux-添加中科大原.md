---
title: Arch Linux 添加中科大源
date: 2023-02-21 06:04:15
description: 换国内源
tags:
    - Arch
    - Linux
---

1. 添加中科大的Arch Linux源

    - 编辑 /etc/pacman.d/mirrorlist ，在文件的最顶端添加
    
        ```bash
        Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
        ```
        
2. 添加中科大的Arch Linux CN源

    - 在 /etc/pacman.conf 文件末尾添加两行
    
        ```bash
        [archlinuxcn]
        Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
        ```
        
    - 然后安装 archlinuxcn-keyring 包以导入 GPG key
    
        ```bash
        sudo pacman -S archlinuxcn-keyring
        ```
