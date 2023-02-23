---
title: Linux 环境变量配置错误处理
date: 2022-10-23 13:25:51
tags:
    - Bash
    - Linux
---
Linux 环境变量配置出错后，提示command not found<!--more-->

处理方法：

```bash
export PATH=$PATH:/usr/bin:/usr/sbin:/bin:/sbin
```

这样处理后，临时生效环境变量，然后修改配置错误的文件例如.bash_profile   /etc/profile .bashrc 等，修改后，执行source生效正确的环境变量

```bash
source /etc/profile --根据实际情况修改后面的文件路径
```

