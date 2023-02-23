---
title: Hexo Inner Link
date: 2022-11-23 20:05:16
description: Markdown 编写的 Hexo 博客文章内部跳转
tags:
    - Hexo
---

- Markdown 编写的 Hexo 博客文章内部跳转，比如说想在文章1中的某个段落内部超链接跳转到文章2

    ```markdown
    {% post_link 文章文件名(不要后缀) 文章标题(可选) %}   
    ```
    
- 如文章文件名为 Hello-World.md

    ```markdown
    {% post_link Hello-World %}
    {% post_link Hello-World 你好世界 %}
    ```

- 如果想做到这样子的效果

    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.23.20.17.03.png)
    
    ```html
    <br/>
    <h3 style="display:flex">
    <span align="left" style="width:50%">
    PRE : {% post_link 初识-MARO 初识 MARO %}
    </span>
    
    <span align="right" style="width:50%">
    NEXT : {% post_link MARO-VM-调度 MARO VM 调度%}
    </span>
    </h3>
    ```

- 单纯的右边

    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.23.20.15.53.png)
    
    ```html
    <br/>
    <h3>
    <div align="right" >
    NEXT : {% post_link MARO-Distibuted-toolkit MARO Distibuted toolkit %}
    </div>
    </h3>
    ```

