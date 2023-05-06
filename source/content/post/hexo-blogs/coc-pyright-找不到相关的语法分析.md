---
title: Coc-pyright 找不到相关的语法分析
date: 2022-11-17 19:09:23
description: github issue 里面的方法是手动生成存根
tags:
    - VIM
    - Python
---

- Coc-pright 是静态语法分析器，而 python 有一些包是没有经过预编译的，比如说 opencv ，所以就会有找不到相关包的语法分析的情况发生

- 首先
    
    ```bash
    pip install mypy
    ```

- 生成 cv2 的 pyi 文件
    
    ```bash
    stubgen -m cv2 -o {cv2-package-folder}
    ```

    - 在我的环境下 {cv2-package-folder} 是
    
        ```bash
        /home/asleep/softwares/conda/conda/envs/ocv/lib/python3.9/site-packages/cv2
        ```
        
    - 执行命令之后会在目录下生成 cv2.pyi 文件
    
</br>

- 将 cv2.pyi 移动到 coc-pyright 的解析目录，成功解析

    ```bash
    cp /home/asleep/softwares/conda/conda/envs/ocv/lib/python3.9/site-packages/cv2/cv2.pyi  /home/asleep/.config/coc/extensions/node_modules/coc-pyright/node_modules/pyright/dist/typeshed-fallback/stdlib
    ```

    


