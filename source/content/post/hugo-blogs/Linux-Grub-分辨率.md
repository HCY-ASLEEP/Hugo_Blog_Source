---
title: "Linux Grub 分辨率"
date: 2023-02-28T22:27:36+08:00
description: Change grub resolution on arch/debian
tags:
    - Arch
    - Debian
    - Linux
---


1. 首先

   ```bash
   sudo vim /etc/default/grub
   ```
2. 修改参数保存

   ```bash
   GRUB_GFXMODE=1920x1080x32
   ```
3. 重新构建 grub
   - Arch

     ```bash
     sudo grub-mkconfig -o /boot/grub/grub.cfg
     ```
   - Debian

     ```bash
     sudo update-grub
     ```


