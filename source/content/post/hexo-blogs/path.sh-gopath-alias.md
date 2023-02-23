---
title: Linux 环境变量配置
date: 2022-10-23 13:25:51
tags:
    - Bash
    - Linux
---


/etc/profile.d/path.sh<!--more-->

```bash
export PATH=$PATH:/home/asleep/softwares/conda/conda/bin
export PATH=$PATH:/home/asleep/softwares/qrcp/qrcp
export PATH=$PATH:/home/asleep/softwares/nodejs/nodejs/bin
export PATH=$PATH:/home/asleep/softwares/baidunetdisk/baidunetdisk/opt/baidunetdisk
export PATH=$PATH:/home/asleep/softwares/adb/adb
export PATH=$PATH:/home/asleep/softwares/wine/wine
export PATH=$PATH:/home/asleep/softwares/neovim/neovim
export PATH=$PATH:/home/asleep/softwares/go/go_path/bin
export PATH=$PATH:/home/asleep/softwares/autossh/autossh

export GOROOT='/home/asleep/softwares/go/go_base/go'
export GOPATH='/home/asleep/softwares/go/go_path'
export GOCACHE='/home/asleep/softwares/go/go_cache'
export GOENV='/home/asleep/softwares/go/go_env'
# export GOMOD='/home/asleep/softwares/go/go_mod'
export GO111MODULE='auto'
# export GOWORK='/home/asleep/softwares/go/go_work'
export GOPROXY='https://goproxy.cn/,direct'
export PATH=$PATH:/home/asleep/softwares/go/go_base/go/bin

# alias vim='vim.tiny'
# alias sudo='sudo '
```

这里只能存 path 和 alias，其他的能在 /etc/bash.bashrc 里面解决就在那里解决，原则！



