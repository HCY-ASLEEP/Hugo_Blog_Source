---
title: "Debian 系统清理"
date: 2024-01-03T09:03:13Z
draft: false
---

#### 删除软件安装包
apt-get下载的安装包会保存在/var/cache/apt/archives目录下，在软件安装完成后，这些安装包不会被删除
- 查看archives文件夹大小
    ```bash
    du -sh /var/cache/apt/archives
    ```

- 删除已卸载软件的安装包
    ```bash
    sudo apt-get autoclean
    ```
    
- 删除所有的软件安装包
    ```bash
    sudo apt-get clean
    ```
    
- 删除孤立的软件（空间足够不建议操作）
    ```bash
    sudo apt-get autoremove
    ```

#### 清理老旧内核
- 查看系统已安装过的内核（deinstall状态可以卸载，其他的建议保留）
    ```bash
    sudo dpkg --get-selections | grep linux
    ```

- 卸载内核
    ```bash
    sudo apt-get purge [要卸载的内核]
    ```
    
#### 删除残余的配置文件
- 查看当前残余的配置文件
    ```bash
    sudo dpkg --list | grep "^rc"
    ```

- 删除残余的配置文件
    ```bash
    sudo dpkg --list | grep "^rc" | cut -d " " -f 3 | xargs sudo dpkg --purge
    ```
