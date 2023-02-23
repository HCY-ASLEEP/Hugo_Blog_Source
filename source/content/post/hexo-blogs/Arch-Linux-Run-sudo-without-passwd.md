---
title: Arch Linux run sudo without passwd
date: 2023-02-11 13:03:02
description: Arch 免密 sudo
tags:
    - Arch
    - Linux
---

- 首先
    
    ```bash
    sudo visudo
    ```
    
- 然后编辑里面的内容

    ```bash
    myname ALL=(ALL) NOPASSWD: ALL
    ```

