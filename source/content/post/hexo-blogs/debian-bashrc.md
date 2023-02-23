---
title: Debian Bashrc 
date: 2022-10-23 13:25:51
description: 环境变量相关
tags:
    - Debian
    - Linux 
    - Bash
---
需要修改 bashrc 的时候，修改 /etc/bash.bashrc，而不是 ~/.bashrc 前者是全局的，下面这一句话就是 /etc/bash.bashrc 末尾的

```bash
source /etc/profile.d/path.sh
```




