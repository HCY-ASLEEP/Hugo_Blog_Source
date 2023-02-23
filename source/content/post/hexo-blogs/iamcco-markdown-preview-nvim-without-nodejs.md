---
title: Vim => iamcco/markdown-preview.nvim without nodejs
date: 2023-02-21 11:33:20
description: 安装离线版本
tags:
    - VIM
---

**[首先是感谢作者的工作，这个是此项目的地址](https://github.com/iamcco/markdown-preview.nvim)**

我使用的是 vim-plug，首先是安装

```vim
Plug 'iamcco/markdown-preview.nvim', { 'do': { -> mkdp#util#install() }, 'for': ['markdown', 'vim-plug']}
```

vim-plug 安装完之后，重启 vim ，打开 .vim 或者 .md 文件，手动安装离线版本

```vim
:call mkdp#util#install()
```
