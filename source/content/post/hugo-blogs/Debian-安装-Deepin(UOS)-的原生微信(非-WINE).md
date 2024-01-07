---
title: "Debian 安装 Deepin(UOS) 的原生微信(非 WINE)"
date: 2024-01-07T05:08:55Z
draft: false
---

## 优麒麟（ukylin）原生微信
在用 Deepin 的星火商店微信之前，我一直是用优麒麟的微信，但是这个优麒麟的微信已经好久没有维护了，以下是这个版本微信的链接，是阿里云镜像上面的，进入网页之后，页内搜索关键字 weixin 即可：
```bash
https://mirrors.aliyun.com/ubuntukylin/pool/partner/
```

## Deepin 星火商店微信
### 尝试安装出现依赖问题
直到有一天我刷到知乎里面有一篇文章说 Linux 微信支持语音通话，是星火商店的，于是我去看了以下，官网写的是支持的，星火商店链接：
```bash
https://www.spark-app.store/store/sort/chat
```
上面有好多个微信，当时第三个才是我们需要的无 WINE 的原生微信，名字叫 ”微信Linux“，链接如下
```bash
https://mirrors.sdu.edu.cn/spark-store-repository/store//chat/store.spark-app.wechat-linux-spark/store.spark-app.wechat-linux-spark_2.1.9_amd64.deb
```
下载下来之后使用命令安装：
```bash
sudo apt install ./store.spark-app.wechat-linux-spark_2.1.9_amd64.deb
```

意料之中出现报错：
```bash
hcy@debian:~/Downloads$ sudo apt install ./store.spark-app.wechat-linux-spark_2.1.9_amd64.deb
正在读取软件包列表... 完成
正在分析软件包的依赖关系树... 完成
正在读取状态信息... 完成
注意，选中 'store.spark-app.wechat-linux-spark' 而非 './store.spark-app.wechat-linux-spark_2.1.9_amd64.deb'
有一些软件包无法被安装。如果您用的是 unstable 发行版，这也许是
因为系统无法达到您要求的状态造成的。该版本中可能会有一些您需要的软件
包尚未被创建或是它们已被从新到(Incoming)目录移出。
下列信息可能会对解决问题有所帮助：

下列软件包有未满足的依赖关系：
 store.spark-app.wechat-linux-spark : 依赖: libssl1.1 但无法安装它
                                      推荐: deepin-elf-verify (>= 0.0.16.7-1) 但无法安装它
                                      推荐: libgconf-2-4 但是它将不会被安装 或 libgconf2-4 但无法安装它
E: 无法修正错误，因为您要求某些软件包保持现状，就是它们破坏了软件包间的依赖关系。
```

好，虽然报错但是知道是哪里出现错误，现在只要找到缺失的包就行，到了这一步我担心的是如果版本冲突又要封装进 docker 里面了

### 安装 libssl1.1
首先在一个 Github Issue 里面找到了如何安装 libssl1.1 的方法：
```bash
https://github.com/zerotier/ZeroTierOne/issues/1802#issuecomment-1416801584
```

在这个 Issue 里面他建议去 Ubuntu 的这个仓库看看：
```uri
http://security.ubuntu.com/ubuntu/pool/main/o/openssl/
```

页内搜索以下，找到多个在不同桌面环境下的这个包，最后我选择的是这个包：
```bash
http://security.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.1-1ubuntu2.1~18.04.23_amd64.deb
```

安装 libssl1.1 顺利，很好：
```bash
hcy@debian:~/Downloads$ sudo apt install ./libssl1.1_1.1.1-1ubuntu2.1~18.04.23_amd64.deb
正在读取软件包列表... 完成
正在分析软件包的依赖关系树... 完成
正在读取状态信息... 完成
注意，选中 'libssl1.1' 而非 './libssl1.1_1.1.1-1ubuntu2.1~18.04.23_amd64.deb'
下列【新】软件包将被安装：
  libssl1.1
升级了 0 个软件包，新安装了 1 个软件包，要卸载 0 个软件包，有 0 个软件包未被升级。
需要下载 0 B/1,303 kB 的归档。
解压缩后会消耗 4,030 kB 的额外空间。
获取:1 /home/hcy/Downloads/libssl1.1_1.1.1-1ubuntu2.1~18.04.23_amd64.deb libssl1.1 amd64 1.1.1-1ubuntu2.1~18.04.23 [1,303 kB]
正在预设定软件包 ...
正在选中未选择的软件包 libssl1.1:amd64。
(正在读取数据库 ... 系统当前共安装有 146588 个文件和目录。)
准备解压 .../libssl1.1_1.1.1-1ubuntu2.1~18.04.23_amd64.deb  ...
正在解压 libssl1.1:amd64 (1.1.1-1ubuntu2.1~18.04.23) ...
正在设置 libssl1.1:amd64 (1.1.1-1ubuntu2.1~18.04.23) ...
正在处理用于 libc-bin (2.36-9+deb12u3) 的触发器 ...
```

### 安装 deepin-elf-verify
接下来是看看 deepin-elf-verify 这个包了，要安装这个包心情更加忐忑，因为网上好多人说这个是锁发行版的，我在星火商店的这个讨论里面找到了答案：
```bash
https://bbs.spark-app.store/d/1120-deepin-elf-verify/2
```

里面有一个用户说，可以去 deepin 的仓库找到这个包：
```bash
https://community-packages.deepin.com/deepin/pool/main/d/deepin-elf-verify/
```

好，现在找到了，我用的是这个链接下载的：
```uri
https://community-packages.deepin.com/deepin/pool/main/d/deepin-elf-verify/deepin-elf-verify_1.2.0.6-1_amd64.deb
```

成功安装：
```bash
hcy@debian:~/Downloads$ sudo apt install ./deepin-elf-verify_1.2.0.6-1_amd64.deb
正在读取软件包列表... 完成
正在分析软件包的依赖关系树... 完成
正在读取状态信息... 完成
注意，选中 'deepin-elf-verify' 而非 './deepin-elf-verify_1.2.0.6-1_amd64.deb'
下列【新】软件包将被安装：
  deepin-elf-verify
升级了 0 个软件包，新安装了 1 个软件包，要卸载 0 个软件包，有 0 个软件包未被升级。
需要下载 0 B/1,408 B 的归档。
解压缩后会消耗 158 kB 的额外空间。
获取:1 /home/hcy/Downloads/deepin-elf-verify_1.2.0.6-1_amd64.deb deepin-elf-verify amd64 1.2.0.6-1 [1,408 B]
正在选中未选择的软件包 deepin-elf-verify。
(正在读取数据库 ... 系统当前共安装有 146598 个文件和目录。)
准备解压 .../deepin-elf-verify_1.2.0.6-1_amd64.deb  ...
正在解压 deepin-elf-verify (1.2.0.6-1) ...
正在设置 deepin-elf-verify (1.2.0.6-1) ...
```

### 最后成功安装星火商店微信
再次安装微信安装包，成功安装
```bash
hcy@debian:~/Downloads$ sudo apt install ./store.spark-app.wechat-linux-spark_2.1.9_amd64.deb
正在读取软件包列表... 完成
正在分析软件包的依赖关系树... 完成
正在读取状态信息... 完成
注意，选中 'store.spark-app.wechat-linux-spark' 而非 './store.spark-app.wechat-linux-spark_2.1.9_amd64.deb'
将会同时安装下列软件：
  gconf-service gconf2-common libgconf-2-4
建议安装：
  spark-dstore-patch | deepin-app-store
下列【新】软件包将被安装：
  gconf-service gconf2-common libgconf-2-4 store.spark-app.wechat-linux-spark
升级了 0 个软件包，新安装了 4 个软件包，要卸载 0 个软件包，有 0 个软件包未被升级。
需要下载 1,839 kB/93.9 MB 的归档。
解压缩后会消耗 356 MB 的额外空间。
您希望继续执行吗？ [Y/n]
获取:1 https://mirrors.tuna.tsinghua.edu.cn/debian bookworm/main amd64 gconf2-common all 3.2.6-8 [1,025 kB]
获取:2 /home/hcy/Downloads/store.spark-app.wechat-linux-spark_2.1.9_amd64.deb store.spark-app.wechat-linux-spark amd64 2.1.9 [92.0 MB]
获取:3 https://mirrors.tuna.tsinghua.edu.cn/debian bookworm/main amd64 libgconf-2-4 amd64 3.2.6-8 [413 kB]
获取:4 https://mirrors.tuna.tsinghua.edu.cn/debian bookworm/main amd64 gconf-service amd64 3.2.6-8 [401 kB]
已下载 1,839 kB，耗时 1秒 (1,741 kB/s)
正在选中未选择的软件包 gconf2-common。
(正在读取数据库 ... 系统当前共安装有 146393 个文件和目录。)
准备解压 .../gconf2-common_3.2.6-8_all.deb  ...
正在解压 gconf2-common (3.2.6-8) ...
正在选中未选择的软件包 libgconf-2-4:amd64。
准备解压 .../libgconf-2-4_3.2.6-8_amd64.deb  ...
正在解压 libgconf-2-4:amd64 (3.2.6-8) ...
正在选中未选择的软件包 gconf-service。
准备解压 .../gconf-service_3.2.6-8_amd64.deb  ...
正在解压 gconf-service (3.2.6-8) ...
正在选中未选择的软件包 store.spark-app.wechat-linux-spark。
准备解压 .../store.spark-app.wechat-linux-spark_2.1.9_amd64.deb  ...
正在解压 store.spark-app.wechat-linux-spark (2.1.9) ...
正在设置 gconf2-common (3.2.6-8) ...

Creating config file /etc/gconf/2/path with new version
正在设置 store.spark-app.wechat-linux-spark (2.1.9) ...
DISTRIB_ID=uos
DISTRIB_RELEASE=20
DISTRIB_DESCRIPTION=UnionTech OS 20
DISTRIB_CODENAME=eagle
正在处理用于 sgml-base (1.31) 的触发器 ...
正在设置 libgconf-2-4:amd64 (3.2.6-8) ...
正在处理用于 libc-bin (2.36-9+deb12u3) 的触发器 ...
正在设置 gconf-service (3.2.6-8) ...
```

### 小问题
最后的最后还有一个小问题，就是，这个微信在哪里启动呢？我在星火商店的讨论里面再次找到了答案：
```bash
https://bbs.spark-app.store/d/755-linux/4
```

这里面说，微信的启动路径在这里：
```bash
ls /opt/apps/store.spark-app.wechat-linux-spark/files/launch.sh
```

至于制作桌面图标（desktop）文件请自行搜索，网上好多教程

### 效果
在 Debian 12 的 KDE Wayland 环境下可以截图，聊天记录搜索，甚至可以语音通话！截止至 2024 年 1 月 7 日，不锁发行版，感谢 Deepin 社区
