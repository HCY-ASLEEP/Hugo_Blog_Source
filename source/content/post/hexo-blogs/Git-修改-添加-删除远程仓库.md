---
title: Git 修改 / 添加 / 删除远程仓库
date: 2023-02-12 04:22:54
description: 多端同步可能会用到
tags:
    - Git
    - Github
---

##### 以下操作都是对管理本地的远端配置

- 修改远程仓库地址
    
    ```bash
    git remote set-url origin <remote-url>
    ```
- 仓库路径查询查询
    
    ```bash
    git remote -v
    ```
- 添加远程仓库

    ```bash
    // 注:项目地址形式为:https://gitee.com/xxx/xxx.git或者 git@gitee.com:xxx/xxx.git
    git remote add origin <你的项目地址> 
    ```
- 删除指定的远程仓库

    ```bash
    git remote rm origin
    ```
    
    
    
    

