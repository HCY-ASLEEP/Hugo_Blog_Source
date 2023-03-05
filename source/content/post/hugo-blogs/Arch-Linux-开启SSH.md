---
title: "Arch Linux 开启SSH"
date: 2023-03-05T13:59:46+08:00
tags:
    - Linux
    - Arch
---

```bash
pacman -Sy openssh                                      # 安装
echo "PermitRootLogin yes" >> "/etc/ssh/sshd_config"    # 修改配置表
systemctl start sshd                                    # 开启
systemctl enable sshd                                   # 开机启动
```

