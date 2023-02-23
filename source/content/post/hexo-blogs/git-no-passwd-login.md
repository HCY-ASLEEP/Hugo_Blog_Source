---
title: Git no passwd login
date: 2023-02-11 11:06:29
description: git 免密码登陆
tags:
    - Git
    - Github
---

- 输入
    
    ```bash
    git config  --global credential.helper store
    ```
    
- 查看配置

    ```bash
    git config --list
    ```
    
- 如果有下面的行，说明配置成功了

    ```bash
    credential.helper=store
    ```
    
- 后面操作的时候只需要输入一次密码之后，就可以免密码操作了，在家目录下会有一个 ".git-credentials" 文件生成



