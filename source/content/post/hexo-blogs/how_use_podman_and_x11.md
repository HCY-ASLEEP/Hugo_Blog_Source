---
title: Podman + X11 环境运行 GUI 程序
date: 2022-12-30 14:27:15
description: 比较麻烦的一种方法(后面还有一种更加方便的方法)
tags:
    - Podman 
---

**podman 运行 GUI 应用**

1. 首先 host 的环境，也就是宿主机的桌面环境得是 X11 ，Wayland 是不行的

2. 允许 podman 访问 xserver

    在 host 上运行
    
    ```
    xhost +"local:podman@"
    ```
    
    如果不行，那就加 sudo 试一下
    
3. 创建并且启动 podman 容器

    这个建议在 rootless 下执行，在 Arch Linux 下测试是可以 rootless 运行这条命令的
    
    ```
    podman run --privileged -it -e DISPLAY=$DISPLAY -v /tmp/.X11-unix/:/tmp/.X11-unix:rw -v /home/hcy/Share/:/share ubuntu
    ```
    
    老规矩，不行就加上 sudo
    
    "-v" 选项是 verbose ，也就是映射，上面第二个要根据自己的情况而定，这个映射是为了宿主与容器共享文件夹
    
    ubuntu 是你需要的 image 名称，可以换成其他的名称
    
    
4. 安装 X11 应用并测试

    完成第三步之后，就已经自动进入到容器交互界面里面了
    
    以 ubunut 为例子，先更新 apt
    
    ```
    apt update
    ```
    
    ```
    apt upgrade
    ```

    安装文字编辑器（非GUI）
    
    ```
    apt install neovim
    ```
    
    安装 sudo
    
    ```
    apt install sudo
    ```

    安装文字编辑器（GUI）
    
    ```
    apt install mousepad
    ```
    
    启动 mousepad ，不出意外应该可以跑起来
    
    ```
    mousepad
    ```
    
    因为有时候并不想在容器的 root 用户下开发，所以有必要创建一个普通用户
    
    ```
    useradd -m hcy
    ```
    
    为 root 重设密码
    
    ```
    passwd root
    ```
    
    为 hcy 重设密码
    
    ```
    passwd hcy
    ```
    
    指定 hcy 的 shell 为 bash 而非 sh
    
    ```
    usermod -s /bin/bash hcy
    ```
    
    将 hcy 添加到 sudo 组里面
    
    ```
    sudo visudo
    ```
    
    找到 "root ALL=(ALL:ALL) ALL"，在这一行下面添加这一行，保存退出
    
    ```
    hcy ALL=(ALL:ALL) ALL
    ```
    
    
    切换到 hcy 用户
    
    ```
    su hcy
    ```
    
    不出意料，会出现如下报错打不开 GUI 应用
    
    ```
    Authorization required, but no authorization protocol specified

    (org.gnome.Nautilus:4410): Gtk-WARNING **: 23:12:51.995: cannot open display: :10.0
    ```
    
    如何解决？这时候需要一条神奇的命令
    
    ```
    xhost + local:
    ```
    
    提示没有 xhost 怎么办？在 ubuntu 下面安装如下组件之后再试一次（老规矩：不行就 sudo ）
    
    ```
    sudo apt install x11-xserver-utils
    ```
    
    不出意外，现在在 hcy 用户下也可以启动 mousepad GUI 了


    如果提示 " Failed to execute child process "dbus-lauch" " 报错
    
    ```
    sudo apt-get install dbus-x11
    ```
    

