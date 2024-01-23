---
title: "Debian Gnome 修改默认目录"
date: 2024-01-23T11:40:20+08:00
draft: false
---

我在 askbuntu 里面找到了答案：

[Change default user folders path](https://askubuntu.com/questions/67044/change-default-user-folders-path)

具体做法：

```bash
vi ~/.config/user-dirs.dirs
```

以下是我的配置：

```toml
# This file is written by xdg-user-dirs-update
# If you want to change or add directories, just edit the line you're
# interested in. All local changes will be retained on the next run.
# Format is XDG_xxx_DIR="$HOME/yyy", where yyy is a shell-escaped
# homedir-relative path, or XDG_xxx_DIR="/yyy", where /yyy is an
# absolute path. No other format is supported.
#
XDG_DESKTOP_DIR="/home/hcy/Desktop/"
XDG_DOWNLOAD_DIR="/home/hcy/Downloads/"
XDG_TEMPLATES_DIR="/home/hcy/Templates/"
XDG_PUBLICSHARE_DIR="/home/hcy/Public/"
XDG_DOCUMENTS_DIR="/home/hcy/Documents/"
XDG_MUSIC_DIR="/home/hcy/Music/"
XDG_PICTURES_DIR="/home/hcy/Pictures/"
XDG_VIDEOS_DIR="/home/hcy/Videos/"
```
