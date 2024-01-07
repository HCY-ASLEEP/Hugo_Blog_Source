---
title: "Linux 图标位置以及配置模板"
date: 2024-01-03T03:23:39Z
draft: false
---

### 系统级图标位置
系统级图标通常位于以下目录：
- 系统默认图标目录
    ```bash
    /usr/share/icons
    ```
- 程序安装时通常会在该目录下添加程序图标
    ```bash
    /usr/share/pixmaps
    ```
- 应用程序的启动器文件通常位于该目录下
    ```bash
    /usr/share/applications
    ```
    
### 用户级图标位置
用户级图标通常位于以下目录：
- 用户自己添加的图标或自己下载的图标可以放在这个目录下
    ```bash
    ~/.local/share/icons
    ```
- 桌面上的图标通常位于这个目录下
    ```bash
    ~/Desktop
    ```
- 用户自己修改的应用程序菜单通常位于这个目录下
    ```bash
    ~/.config/menus
    ```

### desktop 文件模板（以 onlyoffice 为例）
```python
[Desktop Entry]
Name=Onlyoffice
Exec=/opt/only-office/DesktopEditors-x86_64.AppImage
Terminal=false
Type=Application
Icon=/usr/share/icons/hicolor/only-office/onlyoffice.png
StartupWMClass="ONLYOFFICE Desktop Editors"
Comment=Office
Categories=Office;
```
