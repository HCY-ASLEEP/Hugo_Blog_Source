---
title: "让 KDE 可以像 GNOME 一样使用 Meta 键进行后台窗口调度"
date: 2024-01-01T08:09:17Z
draft: false
---

众所周知，KDE Plasma 的 Meta 键也就是 Win 键或者叫 Super 键是很难直接在图形界面的设置里面直接重新调度 Remap,当你尝试要单独去映射 Meta 键的时候总会乱码，所以需要借助命令行的作用来使得 Meta 键位失效或者 Remap

原来的 Meta 键是唤起 KDE 的菜单，要想使得它失效，可以应用以下命令：
```bash
kwriteconfig5 --file kwinrc --group ModifierOnlyShortcuts --key Meta ""
```

如果要将 Meta 映射为激活后台切换调度，也就是图形界面设置里面 KWin 的`"Overview=Meta+W,Meta+W,显示/隐藏桌面总览"`，要使用如下命令：

- 首先是映射：
    ```bash
    kwriteconfig5 --file kwinrc --group ModifierOnlyShortcuts --key Meta "org.kde.kglobalaccel,/component/kwin,,invokeShortcut,Overview"
    ```
    
- 然后是重启 KWin ：
    ```bash
    qdbus org.kde.KWin /KWin reconfigure
    ```

- 找到解决办法的 Reddit 讨论链接：

    ```url
    https://www.reddit.com/r/kde/comments/t416lu/rebind_meta_key_to_open_overview/
    ```

