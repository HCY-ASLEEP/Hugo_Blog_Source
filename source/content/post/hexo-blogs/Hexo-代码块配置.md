---
title: Hexo 代码块配置
date: 2023-02-11 12:13:01
tags:
    - Hexo
---

在 NexT 的主题配置文件里面启用<!--more-->

```yml
codeblock:
  # Code Highlight theme
  # All available themes: https://theme-next.js.org/highlight/
  theme:
    light: default
    dark: tomorrow-night
  prism:
    light: prism
    dark: prism-dark
  # Add copy button on codeblock
  copy_button:
    enable: true
    # Available values: default | flat | mac
    style: flat
```


