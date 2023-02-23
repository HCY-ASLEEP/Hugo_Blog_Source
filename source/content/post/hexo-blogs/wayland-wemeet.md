---
title: Wayland Wemeet
date: 2022-11-05 14:45:11
description: 腾讯会议不支持 Wayland 解决方法
tags:
    - Linux
    - Wayland
---

腾讯会议不支持 Wayland

<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022-04-19-13-25-31屏幕截图.png"/>

- 进入 /opt/wemeet 目录
- 编辑 wemeetapp.sh 文件
- 在 export QT_PLUGIN_PATH="${HERE}/plugins" 后添加如下三行代码后保存
    
    ```bash
    export XDG_SESSION_TYPE=x11
    export QT_QPA_PLATFORM=xcb
    unset WAYLAND_DISPLAY
    ```

- 重启即可
	

###### 

缺点

- 由于 Wayland 的限制，腾讯会议现在无法捕捉到屏幕，自然“共享屏幕”也就失效了，实际效果是当尝试共享屏幕时，共享的是 pure black





