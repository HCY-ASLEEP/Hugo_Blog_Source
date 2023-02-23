---
title: Shell 走代理
date: 2023-02-20 13:26:51
description: Shell bash proxy
tags:
    - Linux
---

我使用的是 clash ，在端口 7890 监听 

- Http 协议
    ```bash
    # export http_proxy=http://proxyAddress:port
    export http_proxy=http://127.0.0.1:7890
    export https_proxy=http://127.0.0.1:7890
    ```

- TCP 协议(socket5, ss, ssr)
    ```bash
    export http_proxy="socks5://127.0.0.1:7890"
    export https_proxy="socks5://127.0.0.1:7890"
    ```
    
- 或者干脆设置所有的代理

    ```bash
    export ALL_PROXY=socks5://127.0.0.1:7890
    ```
- 取消代理 ALL_PROXY ，其他同理

    ```bash 
    unset ALL_PROXY
    ```
