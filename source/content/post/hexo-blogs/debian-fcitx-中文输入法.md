---
title: Debian Fcitx 中文输入法
date: 2022-10-23 13:25:51
tags:
    - Debian
    - Fcitx
    - Linux
---
不是中文环境需要进行切换中文环境<!--more-->

可以通过以下命令切换:

```bash
sudo dpkg-reconfigure locales
```

至少选择zh_CN.UTF-8

更新

```bash
sudo apt update 
```

安装 fcitx

```bash
sudo apt install fcitx
```

安装 google-pinyin

```bash
sudo apt install fcitx-googlepinyin
```

重启

命令行输入 im-config 选中fcitx



