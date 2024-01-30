---
title: Arch KDE 折腾日志
date: 2023-02-21 05:09:57
tags:
    - KDE
    - Arch
    - Linu
---

### live cd 安装使用 WIFI

- 先用命令 ip link查看网络接口

  ```bash
  ip link
  ```

- 查看到我的机器的无线网络接口是wlan0(不同的机器可能名字不同)，这里 wlan0 为例
- 默认是关闭的状态，需要先开启它，而开启它之前还需要先激活它(即取消禁用，我这台机器默认是blockeded，禁用的)

  ```bash
  rfkill unblock wifi    # 取消禁用wifi设备
  ip link set wlan0 up   # 开启wlan0
  ```

- 输入iwctl进入交互式提示符（interactive prompt），配置并连接到互联网

  ````
  station wlan0 scan
  station wlan0 get-networks
  station wlan0 connect <network name>
  station wlan0 show
  exit　　# 回到命令行
  ````

- ping百度可以ping通，就说明已经连接上了互联网

### 免密 sudo

- Arch Linux run sudo without passwd
- 首先

  ```bash
  sudo visudo
  ```

- 然后编辑里面的内容

  ```bash
  myname ALL=(ALL) NOPASSWD: ALL
  ```




### 添加中科大源

1. 添加中科大的Arch Linux源

   - 编辑 /etc/pacman.d/mirrorlist ，在文件的最顶端添加

     ```bash
     Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
     ```


2. 添加中科大的Arch Linux CN源

   - 在 /etc/pacman.conf 文件末尾添加两行

     ```bash
     [archlinuxcn]
     Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
     ```

   - 然后安装 archlinuxcn-keyring 包以导入 GPG key

     ```bash
     sudo pacman -S archlinuxcn-keyring
     ```





### 安装 KDE

1. 若有n卡驱动

   ```bash
   sudo pacman -S nvidia
   ```

2. 安装 plasma

   ```bash
   sudo pacman -S plasma   #完整版
   plasma-meta             #精简版
   plasma-desktop          #极简版，如果是这个版本，记得安装 alacritty 或者 konsole ，并不自带终端
   ```

3. 安装启动界面

   ```bash
   sudo pacman -S sddm         #启动界面
   sudo systemctl enable sddm  # 启用启动界面
   ```


### 中文 locale 编码和字体

1. locale配置

   - 可以执行命令locale查看当前配置

     ```bash
     $ locale
     LANG=en_US.UTF-8
     LANGUAGE=en_US
     LC_CTYPE="en_US.UTF-8"
     LC_NUMERIC=en_US.UTF-8
     LC_TIME=en_US.UTF-8
     LC_COLLATE="en_US.UTF-8"
     LC_MONETARY=en_US.UTF-8
     LC_MESSAGES="en_US.UTF-8"
     LC_PAPER=en_US.UTF-8
     LC_NAME=en_US.UTF-8
     LC_ADDRESS=en_US.UTF-8
     LC_TELEPHONE=en_US.UTF-8
     LC_MEASUREMENT=en_US.UTF-8
     LC_IDENTIFICATION=en_US.UTF-8
     LC_ALL=
     ```

   - 注意：设置LC_ALL变量，会覆盖除了LANGUAGE之外的所有locale环境变量，因此尽量不要使用它

2. 安装中文locale

   - 使用UTF-8的locale，将en_US.UTF-8和zh_CN.UTF-8的注释从配置文件/etc/locale.gen去掉，即删除行首的#

     ```bash
     # /etc/locale.gen
     _US.UTF-8 UTF-8
     _CN.UTF-8 UTF-8
     ```

   - 然后执行

     ```bash
     sudo locale-gen
     ```


3. 配置LANG和LANGUAGE

   - 设置locale全局配置文件/etc/locale.conf ，但不推荐在该文件中配置全局的中文locale，会导致 tty 乱码

     ```bash
     # /etc/locale.conf
     LANG=en_US.UTF-8
     ```

   - 不同的用户可以在下列文件中，设置各自的环境变量

     ```bash
     # ~/.bashrc：每次使用终端登录时读取并运用里面的设置
     # ~/.xinitrc：每次使用 startx 或 SLiM 启动 X 界面时读取并运用里面的设置
     # ~/.xprofile：每次使用 GDM 等显示管理器登录时读取并运用里面的设置
     
     export LANG=zh_CN.UTF-8
     export LANGUAGE=zh_CN:en_US
     ```


4. 安装中文字体

   ```bash
   # 这些是官方仓库里面的文泉驿字体
   hcy@archlinux:~$ sudo pacman -Ss wqy
   community/wqy-bitmapfont 1.0.0RC1-5 [已安装]
       A bitmapped Song Ti (serif) Chinese font
   community/wqy-microhei 0.2.0_beta-11 [已安装]
       A Sans-Serif style high quality CJK outline font
   community/wqy-microhei-lite 0.2.0_beta-10 [已安装]
       The "Light" face of WenQuanYi Micro Hei font family
   community/wqy-zenhei 0.9.45-9 [已安装]
       A Hei Ti Style (sans-serif) Chinese Outline Font.
   ```


### 中文 fcitx5

安装 fcitx5 实现输入中文具体步骤如下

```bash
sudo pacman -S fcitx5-im 
sudo pacman -S fcitx5-chinese-addons
```

其中 fcitx5-chinese-addons 包含了大量中文输入方式：拼音、双拼、五笔拼音、自然码、仓颉、冰蟾全息、二笔等

然后在环境变量配置文件 /etc/environment 中添加如下内容

```bash
# /etc/environment
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx
```

然后配置自动启动，提供以下3种方法，第一个不行，试第2个，然后第3个

1. 如果支持XDG桌面环境，例如KDE,GNOME, Xfce,默认重启后即可
2. 在~/.config/autostart目录下添加fcitx-autostart.desktop文件，可以从目录etc/xdg/autostart中复制
3. 对于i3-wm,可以在配置文件~/.config/i3/config中添加如下代码

   ```bash
   # fcitx5
   exec_always --no-startup-id fcitx5
   ```


### Fcitx5 Alacritty 显示问题

安装完成 fcitx5 中文输入法之后，其他界面都可以正常使用，就是 alacritty 不能使用，就连激活都没有办法激活

[**直到我看到了这一篇博客**](https://www.csslayer.info/wordpress/linux/use-plasma-5-24-to-type-in-alacritty-or-any-other-text-input-v3-client-with-fcitx-5-on-wayland/)

只需要在 KDE Config 里面开启 Virtual Keyboard 就行了

![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/arch-kde-fcitx5-image-1.png)

然后就可以愉快地享受 alacritty 啦

### 开启 SSH

```bash
pacman -Sy openssh                                      # 安装
echo "PermitRootLogin yes" >> "/etc/ssh/sshd_config"    # 修改配置表
systemctl start sshd                                    # 开启
systemctl enable sshd                                   # 开机启动
```

### 配置 VNC 远程连接

在本文写的时候，VNC 远程连接 Wayland 显示协议只能说非常不成熟，甚至有点离谱，键盘压根输入不了，颜色反转，声音不能正常 pipewire 远程，因此如果将自己的桌面环境作为 VNC 远程后端，还是 X11/Xorg 协议体验好很多。与此同时，桌面环境里面，Xfce4 兼顾了流畅性与桌面功能健全性，虽然说 KDE 也是很健全的，但是 KDE 的动画在 VNC 远程里面反而是消耗资源的问题所在，远程体验不流畅。

1. 使用一个 GUI 的 VNC Server Manager：

   ```bash
   # KDE Remote Frame Buffer ，但是也是适用与其他桌面环境如 Xfce4 的
   sudo pacman -S krfb
   ```

2. 我是先安装了 KDE 和 SDDM 显示管理器再安装的 Xfce4

   ```bash
   sudo pacman -S xfce4
   ```

   安装完 Xfce4 之后在 SDDM 的左下角就可以选择 Xfce4 还是 KDE 桌面

3. krfb 配置

   ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-35.png)

   ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-37.png)

   ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-38.png)

   ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-38_1.png)

   ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-39.png)

   ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2023-03-10_22-39_1.png)

4. VNC 客户端推荐：

   - Windows TigerVNC
   - Linux krdc(KDE Remote Desktop Client)
   - Android bVNC

5. 有一些包是其他教程里面推荐的，不过我觉得应该不用

   ```bash
   sudo pacman -S xfce4-goodies   # Xfce4 包组（group）
   sudo pacman -S xorg xorg-xinit
   sudo pacman -S xdg-desktop-portal
   ```


### 删除软件

我使用的是 archinstall 来安装 Arch KDE 的，在安装的时候是以 plasma-meta 整体安装，所以想卸载 discover 等组件的时候就会破坏依赖，所以采取下面的方法来卸载

```bash
sudo pacman -Rdd discover                           # Software market
sudo pacman -Rdd plasma-welcome
sudo pacman -Rdd plasma-systemmonitor
sudo pacman -Rdd drkonqi                            # Crash reporter
```

这种卸载方式只是单独卸载目标软件包，虽然在下次升级 plasma-meta 的时候还是会安装回来的，不过对我问题不大，升级之后再卸载就行

如果一个包可以安全被卸载，想卸载干净的话 (卸载只是被它依赖的依赖)

```bash
sudo pacman -Rsn <target>
```

### 如何修复 Discover

虽然我已经卸载 Discover 了，但是还是需要记录一下我是如何修复 Discover 的

[**"感谢 Reddit 再次帮我排忧解难"**](https://www.reddit.com/r/kde/comments/85642m/kde_discover_not_working/)

```bash
# 只需要一步
sudo pacman -S packagekit-qt5
```