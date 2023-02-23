---
title: Hexo next favicon
date: 2023-02-11 11:01:48
description: 设置网站的图标
tags:
    - Hexo
---

- 把图标放在 hexo\source\images 目录下，这个目录没有可以自己手动创建

- 修改 next主题配置文件，\next\themes\next\_config.yml 文件
    ```yml
    favicon:
      small: /images/favicon-16x16-next.png
      #medium: /images/favicon-32x32-next.png
      medium: /images/favicon.ico
      apple_touch_icon: /images/apple-touch-icon-next.png
      safari_pinned_tab: /images/logo.svg
      #android_manifest: /images/manifest.json
      #ms_browserconfig: /images/browserconfig.xml
    ```
    

