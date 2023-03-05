---
title: "SSH 允许非root用户登录"
date: 2023-03-05T13:53:48+08:00
tags:
    - Linux
---

通过配置`/etc/ssh/sshd_config`文件的`AllowUsers`，可以添加多个允许登录的用户名，不同用户名之间用空格分开，不在其中的用户则不可以登录

```bash
AllowUsers [username]
```

例如只想允许john和teena两个用户通过ssh登录，其余用户均不允许

```bash
AllowUsers john teena
```

执行下面的命令，重启sshd服务使配置生效

```bash
service sshd restart
```
