---
title: "Podman 换源"
date: 2023-03-19T19:50:14+08:00
tags:
    - Podman
    - Linux
---

1. Podman 默认注册表配置文件在 /etc/containers/registries.conf

2. 国内的镜像源有:

    - docker官方中国区 https://registry.docker-cn.com
    - 网易 http://hub-mirror.c.163.com
    - USTC http://docker.mirrors.ustc.edu.cn
    - 阿里云 http://<你的ID>.mirror.aliyuncs.com

3. 修改为以下内容:
    
    ```bash
    # 这个是添加 dockerhub 的搜索源
    unqualified-search-registries = ["docker.io"]
    
    [[registry]]
    prefix = "docker.io"
    location = "xxxxxxxx.mirror.aliyuncs.com"
    ```
