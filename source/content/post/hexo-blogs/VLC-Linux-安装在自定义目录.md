---
title: VLC Linux 安装在自定义目录
date: 2022-11-05 15:16:56
tags: 
    - Linux
---

apt 只下载包及其依赖而不安装

```bash
# sudo apt-get install -d <软件包>
sudo apt-get install -d vlc
```
<!--more-->
这将会下载到这个目录

```bash
/var/cache/apt/archives/
```

为了只是获得想要的包和依赖，应该先清空这个目录再下载

将 **/var/cache/apt/archives/** 的包以及依赖移到某一个目录保存

然后把这些包安装到指定目录

```bash
for file in packagesPath:
do 
	echo $file
	sudo dpkg -x $file customInstallPath
done
```

在 customInstallPath 下编写一个启动脚本

```bash
#!/bin/sh
HERE="$(dirname "$(readlink -f "$0")")"
export UNION_PRELOAD=$HERE
export LD_PRELOAD=$HERE/libunionpreload.so
export PATH=$HERE/usr/bin/:$HERE/usr/sbin/:$HERE/usr/games/:$HERE/bin/:$HERE/opt/vlc/:$HERE/sbin/:$PATH
export LD_LIBRARY_PATH=/usr/lib/x86_64-linux-gnu/:$HERE/usr/lib/:$HERE/usr/lib/x86_64-linux-gnu/:$HERE/lib/:$HERE/lib/x86_64-linux-gnu/:$HERE/usr/lib/x86_64-linux-gnu/vlc/:$LD_LIBRARY_PATH
export QT_PLUGIN_PATH=/usr/lib/x86_64-linux-gnu/qt5/plugins/:$HERE/usr/lib/x86_64-linux-gnu/qt5/plugins/:$QT_PLUGIN_PATH
export XDG_DATA_DIRS=$HERE/usr/share/:$XDG_DATA_DIRS
exec $HERE/usr/bin/vlc "$@"
```

保存并赋予这个启动脚本执行权限

```bash
chmod a+x launch.sh
```

执行 launch.sh 就可以运行 vlc 了

这个脚本里面的 bash 变量是程序内部执行需要的变量，并不是环境变量，只有知道软件构建运行的源码才可以写出来，所以这个脚本并不是通用的，只适合 vlc




