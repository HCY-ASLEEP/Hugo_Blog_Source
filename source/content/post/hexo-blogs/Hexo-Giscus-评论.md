---
title: Hexo Giscus 评论 (NexT 主题)
date: 2023-02-12 06:24:19
description: 让你的 Github Pages 也可以像 WordPress 一样评论！
tags:
    - Hexo 
    - Giscus
---

##### 详情请参考官方网站

- Giscus 官方网站
    ```url
    https://giscus.app/zh-CN
    ```


- Hexo-next-giscus 官方仓库

    ```url
    https://github.com/next-theme/hexo-next-giscus
    ```
    
- 参考的博客，不保证链接长期有效

    ```url
    https://getiot.tech/knowledge-base/hexo/hexo-comments
    ```
    
    看域名，这个有可能在大陆被 ban 掉，科学上网
    ```url
    https://ithelp.ithome.com.tw/articles/10306883
    ```
    
- 自己实践过的步骤

    - 准备工作
    
        1. 你的仓库必须是公开的 (public)，否则访客将无法查看 discussion

        2. 你的 GitHub 已安装<a herf="https://github.com/apps/giscus"> giscus app </a>，否则访客将无法评论和回应
        
        3. 在你的仓库中启用<a herf="https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/enabling-or-disabling-github-discussions-for-a-repository"> Discussions </a>功能
        
        
    - Hexo 配置
    
        - 在你的 Hexo 博客目录中执行以下命令，安装 hexo-next-giscus 插件
        
            ```bash
            npm install hexo-next-giscus --save
            ```
            
        - 然后在 Hexo 或者 theme 的 _config.yml (我的是 _config.next.yml) 配置文件添加如下内容
        
            ```yml
            giscus:
              enable: false
              repo: # Github repository name
              repo_id: # Github repository id
              category: # Github discussion category
              category_id: # Github discussion category id
              # Available values: pathname | url | title | og:title
              mapping: pathname
              # Available values: 0 | 1
              reactions_enabled: 1
               # Available values: 0 | 1
              emit_metadata: 1
              # Available values: light | dark | dark_high_contrast | transparent_dark | preferred-color-scheme
              theme: light
              # Available values: en | zh-CN
              lang: en
              # Place the comment box above the comments
              input_position: bottom
              # Load the comments lazily
              loading: lazy
            ```
    
    - Giscus 配置
        
        - 在 https://giscus.app 页面根据你的个人喜好勾选 giscus 配置项，进行配置，会生成一个配置脚本
        
            - 在填仓库名字的时候要注意有没有空格（呜呜呜，因为这个搞了好久）
            
            - 配置脚本就是那一段 `<script ... > ... </script>`
        

        - 参考该脚本内容修改 _config.yml 文件的 giscus 配置项
        
        
    - 部署
    
        ```bash
        hexo g
        hexo s
        ```
        
    - 浏览器打开 http://localhost:4000，在文章末尾可以看到 giscus 评论区
