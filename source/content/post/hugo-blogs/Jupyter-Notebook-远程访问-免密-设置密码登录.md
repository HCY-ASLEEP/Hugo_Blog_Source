---
title: "Jupyter Notebook 远程访问 免密 设置密码登录"
date: 2023-03-05T13:39:33+08:00
tags: 
    - Jupyter
---

- 免密码登录
    
    ```bash
    jupyter notebook --ip='*' --NotebookApp.token='' --NotebookApp.password=''
    ```
    
    - 其中 ___... --ip='*' ...___ 表示允许所有 ip 访问而不仅是本机的 127.0.0.1 ，从而达到远程连接的效果
        
- 远程连接但是加上一点点安全性

    ```bash
    jupyter notebook --ip='*' --NotebookApp.token='hcy'
    ```
    
    - 设置 token 为 hcy ，远程访问的时候如下访问
    
        ```bash
        # {jupyter notebook ip}:8888/?token=hcy 
        http://172.35.40.120:8888/?token=hcy
        ```
