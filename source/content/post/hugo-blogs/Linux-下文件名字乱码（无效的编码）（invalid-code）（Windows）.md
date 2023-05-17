---
title: "Linux 下文件名字乱码（无效的编码）（invalid Code）（Windows）"
date: 2023-05-17T17:13:58+08:00
tags:
    - Linux
draft: false
---

文件是在Windows 下创建的，Windows 的文件名中文编码默认为GBK，而Linux中默认文件名编码为UTF8，由于编码不一致所以导致了文件名乱码的问题，解决这个问题需要对文件名进行转码

安装 convmv

```bash
sudo apt-get install convmv
```

convmv 使用方法：

```bash
convmv -f 源编码 -t 新编码 [选项] 文件名
```

常用参数：

|   |   |
|---|---|
|-r|递归处理子文件夹|
|--notest|真正进行操作，默认情况下是不对文件进行真实操作|
|--list|显示所有支持的编码|
|--unescap|可以做一下转义，比如把%20变成空格|

Eg:

```bash
convmv -f GBK -t UTF-8 --notest –-unescap *.mp3
```

</br>

参考链接👉 [https://www.shuzhiduo.com/A/RnJWmlWvdq/](https://www.shuzhiduo.com/A/RnJWmlWvdq/)
