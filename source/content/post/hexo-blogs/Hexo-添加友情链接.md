---
title: Hexo 添加友情链接
date: 2023-02-12 01:32:23
description: 骚操作--自定义友链页面
tags:
    - Hexo
---

##### 给友链新增一个单独的页面

- 命令创建页面
    
    ```bash
    hexo new page links
    ```

- 也可在博客根目录 /source 下手动创建 links 文件夹和里边的 index.md 文件

- 然后在博客根目录 /source 下会生成一个 links 文件夹，打开其中的 index.md 文件，在头部写入 type = “links”，这个一定要写

    ```bash
    ---
    title: 友情链接
    date: 2019-12-08 03:21:39
    type: "links"
    ---
    ```

- 主题配置文件中menu下添加(我的是 NEXT 主题)
    
    ```bash
    Links: /links/ || fa fa-link
    ```
- 在 /themes/next/languages/zh-Hans.yml 文件中 menu 下增加中文描述(可选）
    
    ```bash
    links: 友链    
    ```
- 像普通的 markdown 那样子编辑 index.md 就可以了



