---
title: "SSH连接Docker容器"
date: 2023-03-06T15:29:12+08:00
tags:
 - Linux
 - SSH
---

 1. 创建容器并且端口映射以及后台运行

    ```bash
    docker run -it -d -p 9009:22 ubuntu /bin/bash
    ```
 2. 查看containerId (或者containerAlias)

    ```bash
    docker ps -a
    ```
 3. 进入容器

    ```bash
    # 这里的 containerId 换成 containerAlias 也是可以的
    docker start containerId
    docker exec -it containerId /bin/bash  
    ```
 4. ubuntu 更新源列表

    ```bash
    apt-get update
    ```
 5. 安装ssh-client、ssh-server

    ```bash
    apt-get install openssh-client
    apt-get install openssh-server
    # 安装 neovim 工具
    apt-get install neovim
    ```
 6. 编辑 sshd_config 文件

    ```bash
    vim /etc/ssh/sshd_config
    ```
 7. 允许 ssh 以 root 用户登录

    ```bash
    PermitRootLogin yes
    ```
 8. 设置 root 密码，同时也是设置 ssh root登录的密码

    ```bash
    passwd root
    ```
 9. 安装完成后，先启动ssh服务

    ```bash
    /etc/init.d/ssh start
    ```
10. 查看是否正确启动

    ```bash
    ps -e | grep ssh
    ```
11. 由于将 container 的 22 端口映射到了 host 的 9009 端口，所以可以以下登录

    ```ssh
    ssh root@host_ip -p 9009
    ```


