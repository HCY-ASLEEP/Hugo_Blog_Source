---
title: Podman GUI Wayland/X11
date: 2023-02-11 08:32:39
description: 最简单的一种方法！
tags:
    - Podman
    - Linux
    - Wayland
---

    
1. 首先打开终端（我的容器使用 Linux 主机下的 podman cli 终端操作）

</br>

2. 允许容器访问 xhost，在终端里面输入
    
    ```bash
    xhost +
    ```
</br>

3. run 容器即可
    
    ```bash
    podman run -it --net=host -e DISPLAY=:0 --name=devenv -v /home/hcy/Gateway/:/home/devenv/Gateway devenv /bin/bash
    ```
    
    
    参数解释：

     
    
    ```bash
    ... -it ...  /bin/bash  // 进入交互模式 
    ```

    
    ```bash
    ... --net=host ...  // 允许访问 host 网络 
    ```
    
    
    ```bash
    ... -e DISPLAY=:0 ...   // 使用 host 的显示 
    ```
    
    ```bash
    ... --name=devenv ...   // 指定启动之后容器的名字
    ```
    
    ```bash
    ... -v /home/hcy/Gateway/:/home/devenv/Gateway ...  
    
        // 将 host 的 /home/hcy/Gateway/ 映射到 container 的 /home/devenv/Gateway
    ```
    
    ```bash
    ... devenv ...  // 镜像名字 
    ```
    
    

