---
title: Hexo 加强 Next Theme 美化
date: 2022-10-26 11:27:15
description: 选择用户基数大的 Next 更加容易解决问题
tags:
    - Hexo
---
- Hexo-Next 主题
    hexo-theme-next 应该是目前最广泛使用的hexo主题
    
- 安装 Hexo-Next 主题
    切换到你的博客顶级工程目录，npm 安装
    
    ```bash
    npm install hexo-theme-next
    ```
    
- 切换到 Next 主题
    在你的博客顶层工程目录下打开 "_config.yml"
    
    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.10.26.13.24.11.png)
    
    搜索themes，将里面的值改为next
    
    ```yaml
    theme: next
    ```
- 配置 Next 主题
    将 node_modules/hexo-theme-next/_config.yml 复制到博客顶层文件目录，重命名为 "_config.next.yml"
    
    ```bash
    cd your_site_dir
    cp node_modules/hexo-theme-next/_config.yml  _config.next.yml
    ```
    - 选择 Schemes
    	打开 "_config.next.yml" ，首先可以看到 Scheme Settings ，里面提供了四种模式，本站使用 Mist 主题
    
    	```yaml
    	# Schemes
    	#scheme: Muse
    	scheme: Mist
    	#scheme: Pisces
    	#scheme: Gemini
    	```
    - 设置站点 icon
    	在 favicon 中，可以设置侧边栏头像以及站点 icon ，需要把你的 icon 放在主题目录的 source/img/ 目录下
    	```yaml
    	favicon:
          small: /img/avatar.jfif
          medium: /img/avatar.jfif
          apple_touch_icon: /img/avatar.jfif
          safari_pinned_tab: /images/logo.svg
    	```
    - 还有其余的很多配置，可以参考 "_config.next.yml" 里面的提示来配置
    
    
