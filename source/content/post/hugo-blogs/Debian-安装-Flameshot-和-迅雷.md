---
title: "Debian 安装 Flameshot 和迅雷以及 Neovim 剪切板"
date: 2024-01-07T07:07:48Z
draft: false
---

### Flameshot
直接从仓库安装 flameshot
```bash
sudo apt install flameshot
```

要在 Wayland 下面可以复制粘贴图片还需安装：
```bash
sudo apt install xdg-desktop-portal xdg-desktop-portal-kde wl-clipboard
```

Flameshot 的 GitHub 仓库 Issue 里面有讨论这个问题
```bash
https://github.com/flameshot-org/flameshot/issues/2848
```

### Neovim 剪切板
和上面的 Flameshot 需要 wl-clipboard 一样，在 X11 环境里面也需要：
```bash
sudo apt install xclip
```

### 迅雷
在优麒麟的仓库里面安装
```bash
https://mirrors.aliyun.com/ubuntukylin/pool/partner/com.xunlei.download_1.0.0.1_amd64.deb
```

安装之后尝试启动提示错误
```bash
error while loading shared libraries: libdbus-glib-1.so.2
```

从软件仓库直接安装缺失
```bash
sudo apt-get install libdbus-glib-1-2
```
