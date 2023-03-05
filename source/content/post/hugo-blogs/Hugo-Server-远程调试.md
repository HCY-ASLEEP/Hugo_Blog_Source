---
title: "Hugo Server 远程调试"
date: 2023-03-03T19:54:56+08:00
tags: 
    - Hugo
---

***hugo server*** 命令一般是在 127.0.0.1 启动服务，而使用以下命令就可以远端预览 ***hugo server*** 的结果

```bash
hugo server --bind=0.0.0.0 --baseURL={启动服务的机器的IP}
```
比如说我在服务器写了 passage ，在服务器 `hugo server` ，想在本地浏览器看到 server 效果，这时候 {启动服务的机器的IP} 就是服务器的 IP

我在内网远端预览

```bash
hugo server --bind=0.0.0.0 --baseURL=172.29.40.118
```

