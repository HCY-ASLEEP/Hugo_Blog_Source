---
title: Docker (Podman) MySQL
date: 2023-02-11 13:27:00
description: Docker 安装、启动 MySQL 
tags:
    - MySQL
    - Podman
---

1. 在docker仓库中搜索mysql的镜像
    
    ```bash
    docker search mysql  
    ```
    
2. 下载镜像

    ```bash
    docker pull mysql
    ```
    
3. 启动

    - 参数 -p 设置端口，--name 取名 ，-e MYSQL_ROOT_PASSWORD=123456 设置 账号为 root ，密码为 123456 ，-d 表示作为一个守护进程在后台运行

        ```bash
        docker run -p 3306:3306 --name JY_mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql 
        ```
        
4. 宿主机连接docker中的mysql

    - 错误的连接方式
    
        ```bash
        $ mysql -u root -p
        Enter password: 
        ERROR 2002 (HY000): Can't connect to local MySQL server through socket 
        '/var/run/mysqld/mysqld.sock' (2)
        # 可以看出这样会报错     
        ```
        
    - 正确的连接方式
    
        ```bash
        $ mysql -h 127.0.0.1 -u root -p
        Enter password: 
        Welcome to the MySQL monitor.  Commands end with ; or \g.
        Your MySQL connection id is 9
        Server version: 5.7.26 MySQL Community Server (GPL)
        
        Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.
        
        Oracle is a registered trademark of Oracle Corporation and/or its
        affiliates. Other names may be trademarks of their respective
        owners.
        
        Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
        
        mysql> 
         
        ```
5. 在docker容器中连接宿主机中的mysql

    - 查看宿主机和docker之间的桥接ip
    
        ```bash
        $ ifconfig
        docker0: flags=4163&lt;UP,BROADCAST,RUNNING,MULTICAST&gt;  mtu 1500
                inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
                inet6 fe80::42:8aff:febc:8533  prefixlen 64  scopeid 0x20&lt;link&gt;
                ether 02:42:8a:bc:85:33  txqueuelen 0  (以太网)
                RX packets 4779  bytes 11624681 (11.6 MB)
                RX errors 0  dropped 0  overruns 0  frame 0
                TX packets 6006  bytes 441594 (441.5 KB)
                TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        
        # 宿主机在与容器同一局域网的IP地址一般是docker0对应的IP地址段的首个地址
        即（172.17.0.1 ）
        ```
        
    - 在容器中连接宿主机的mysql
    
        ```bash
        $ mysql -h 172.17.0.1 -u root -p
        ```
        

