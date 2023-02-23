---
title: Nodejs 换源
date: 2022-10-23 13:25:51
tags:
    - Nodejs
---

由于 Node 的官方模块仓库网速太慢，模块仓库需要切换到阿里的源<!--more-->

```bash
npm config set registry https://registry.npm.taobao.org/
```

执行下面的命令，确认是否切换成功

```bash
npm config get registry
```



