---
title: Debian Nvidia 驱动
date: 2022-10-23 13:25:51
tags:
    - Debian
    - Nvidia
    - Linux
---
最重要的一点，去 bios 那里关闭 secure boot 先！<!--more-->

更新

```bash
sudo apt install nvidia-driver
```

重启

```bash
sudo reboot
```

验证是否安装成功

```bash
nvidia-smi
```



