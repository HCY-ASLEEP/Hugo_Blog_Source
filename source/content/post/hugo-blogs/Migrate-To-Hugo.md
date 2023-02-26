---
title: "Migrate to Hugo"
date: 2023-02-26T15:07:16+08:00
description: 如何从 Hexo 迁移到 Hugo
Tags:
    - Hugo
    - Hexo
---

**引言**

起初我是使用 Hexo+NexT 来搭建博客的，我对 Hexo 不满意的地方有两个，一个是生成速度，另外一个是依赖太多，以至于我都要把我的 Hexo 环境打包成 docker 分发到每一个写博客的机子。而 Hugo 就完美解决了这两个问题，可也并不是说 Hugo 毫无缺点。Hugo 最大的缺点是生态尚未完善，看着 github 上面那些 star 数量都不超过一千的主题实在是不太敢使用。但是 Hugo 的生成速度足以实时预览，这个是 Hexo 难以匹敌的。同时迁移成本也是我从 Hexo 迁移到 Hugo 的考虑之一，几乎没有迁移成本让我瞬间有了动力，要是说仅有的迁移成本，那也是 Hexo 的站内链接不适用而已。我不用考虑图片迁移问题，毕竟我是使用 Githb+Picgo 来进行图床存储的。如果你是一个 Rust 粉，你也可以考虑 Zola ，只不过 Zola 的生态更加贫瘠，对于我这种前端小白来说不太友好😭


**感谢为爱发电的开源作者们**


👉 [Picgo Github 仓库](https://github.com/Molunerfinn/PicGo)

👉 [Hugo 官方中文文档](https://www.gohugo.org/)

👉 [Hugo Papermod Theme Github 仓库](https://github.com/adityatelange/hugo-PaperMod)


**正文**

1. 我觉得 config.yaml 比文字说明更加说明配置

    ```yaml
    baseURL: "https://hcy-asleep.github.io/"
    title: Memos
    paginate: 5
    theme: PaperMod
    
    enableRobotsTXT: true
    buildDrafts: false
    buildFuture: true
    buildExpired: false
    
    googleAnalytics: UA-123-45
    
    minify:
      disableXML: true
      minifyOutput: true
    
    params:
      env: production # to enable google analytics, opengraph, twitter-cards and schema.
      title: HCY-BLOGS
      description: "Welcome"
      keywords: [Blog, Portfolio, PaperMod]
      author: HCY
      # author: ["Me", "You"] # multiple authors
      images: [""]
      DateFormat: "January 2, 2006"
      defaultTheme: dark # dark, light
      disableThemeToggle: true
    
      ShowReadingTime: true
      ShowShareButtons: false
      ShowPostNavLinks: true
      ShowBreadCrumbs: true
      ShowCodeCopyButtons: true
      ShowWordCount: true
      ShowRssButtonInSectionTermList: true
      UseHugoToc: true
      disableSpecial1stPost: false
      disableScrollToTop: false
      comments: true
      hidemeta: false
      hideSummary: false
      showtoc: false
      tocopen: false
    
      assets:
        disableHLJS: true # to disable highlight.js
        # disableFingerprinting: true
        favicon: "https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/hcy_site_favicon.png"
        favicon16x16: "https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/hcy_site_favicon.png"
        favicon32x32: "https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/hcy_site_favicon.png"
        apple_touch_icon: "https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/hcy_site_favicon.png"
        safari_pinned_tab: "https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/hcy_site_favicon.png"
    
      label:
        text: "主页"
        icon:
        iconHeight: 35
    
      # profile-mode
      profileMode:
        enabled: false # needs to be explicitly set
        title: ExampleSite
        subtitle: "This is subtitle"
        imageUrl: "<img location>"
        imageWidth: 120
        imageHeight: 120
        imageTitle: my image
        buttons:
          - name: Posts
            url: posts
          - name: Tags
            url: tags
    
      # home-info mode
      homeInfoParams:
        Title: "Hallo~ \U0001F44B"
        Content: "Welcome to my blog"
    
      socialIcons:
        # - name: twitter
        #   url: "https://twitter.com/"
        # - name: stackoverflow
        #   url: "https://stackoverflow.com"
        - name: github
          url: "https://github.com/HCY-ASLEEP"
        - name: email
          url: "mailto:2420066864@qq.com"
    
      analytics:
        google:
          SiteVerificationTag: "XYZabc"
        bing:
          SiteVerificationTag: "XYZabc"
        yandex:
          SiteVerificationTag: "XYZabc"
    
      cover:
        hidden: true # hide everywhere but not in structured data
        hiddenInList: true # hide on list pages and home
        hiddenInSingle: true # hide on single page
    
      editPost:
        URL: "https://github.com/HCY-ASLEEP"
        Text: " Follow me" # edit text
        appendFilePath: false # to append file path to Edit link
    
      # for search
      # https://fusejs.io/api/options.html
      fuseOpts:
        isCaseSensitive: false
        shouldSort: true
        location: 0
        distance: 1000
        threshold: 0.4
        minMatchCharLength: 0
        keys: ["title", "permalink", "summary", "content"]
    menu:
      main:
        - identifier: categories
          name: 目录
          url: /categories/
          weight: 20
        - identifier: tags
          name: 标签
          url: /tags/
          weight: 10
        - identifier: search
          name: 🔍
          url: /search/
          weight: 30
        - identifier: archives
          name: 归档
          url: /archives/
          weight: 10
        - identifier: about
          name: 关于
          url: /about/
          weight: 30
        - identifier: friends
          name: 友链
          url: /friends/
          weight: 29
    # Read: https://github.com/adityatelange/hugo-PaperMod/wiki/FAQs#using-hugos-syntax-highlighter-chroma
    pygmentsUseClasses: true
    markup:
      highlight:
        # noClasses: false
        # anchorLineNos: true
        codeFences: true
        guessSyntax: true
        lineNos: true
        style: monokailight
    
      goldmark:
        renderer:
          unsafe: true
    
    
    outputs:
        home:
            - HTML
            - RSS
            - JSON # is necessary
    
    disablePathToLower: true
    
    permalinks:
        post: /:slug/
        page: /:slug/
        
    pygmentsOptions: linenos=table
    ```
    
    - 代码块显示行数(Code block line number display)
    
        ```yaml
        # config.yaml
        pygmentsOptions: linenos=table
        ```
        
    - Hexo 生成的 URL 是和文章标题一一对应的，而 Hugo 会默认将标题的首字母小写，本来就是小站长，好不容易被收录的网站岂能丢掉? 一句话禁止 URL 自动首字母小写
    
        ```yaml
        # config.yaml
        disablePathToLower: true
        ```
        
    - SEO 优化，让网站结构更加扁平化
    
        ```yaml
        # config.yaml
        permalinks:
            post: /:slug/
            page: /:slug/
        ```
        
    - 允许在 markdown 里面嵌入 HTML
    
        ```yaml
        # config.yaml
        markup:
          goldmark:
            renderer:
              unsafe: true
        ```
        
    - 评论系统(Giscus)
        
        ```yaml
        # config.yaml
        params:
          comments: true
        ```
        创建指定位置的 comments.html 文件
        
        ```vim
        site_source/
        | archetypes/
        | assets/
        | config/
        | content/
        | data/
        | layouts/
        | | _default/
        | | partials/
        | | | comments.html
        | | | extend_head.html
        | public/
        | resources/
        | static/
        | themes/
        ```
        
        comments.html 文件里面写入(尚未配置仓库和/或分类这些字段之前，不会显示这些字段的值)
        
        👉 [Giscus 详细字段获取](https://giscus.app/zh-CN)
        
        ```html
        <script src="https://giscus.app/client.js"
                data-repo="[在此输入仓库]"
                data-repo-id="[在此输入仓库 ID]"
                data-category="[在此输入分类名]"
                data-category-id="[在此输入分类 ID]"
                data-mapping="pathname"
                data-strict="0"
                data-reactions-enabled="1"
                data-emit-metadata="0"
                data-input-position="bottom"
                data-theme="preferred_color_scheme"
                data-lang="zh-CN"
                crossorigin="anonymous"
                async>
        </script>
        ```
        
    
        
    - 支持放大图片(Zoom images)
        
        👉 [解决方法源自 Github Issues](https://github.com/adityatelange/hugo-PaperMod/issues/384)
        
        创建指定位置的 render-image.html 文件
        
        ```vim
        site_source/
        | archetypes/
        | assets/
        | config/
        | content/
        | data/
        | layouts/
        | | _default/
        | | | _markup/
        | | | | render-image.html
        | | partials/
        | public/
        | resources/
        | static/
        | themes/
        ```
        render-image.html 文件里面写入
        
        ```html
        <!-- Checks if page is part of section and page is not section itself -->
        {{- if and (ne .Page.Kind "section") (.Page.Section ) }}
            <!-- Generate a unique id for each image -->
            {{- $random := (substr (md5 .Destination) 0 5) }}
            <input type="checkbox" id="zoomCheck-{{$random}}" hidden>
            <label for="zoomCheck-{{$random}}">
                <img class="zoomCheck" loading="lazy" decoding="async"
                    src="{{ .Destination | safeURL }}" alt="{{ .Text }}"
                    {{ with.Title}} title="{{ . }}" {{ end }} />
            </label>
        {{- else }}
            <img loading="lazy" decoding="async" src="{{ .Destination | safeURL }}"
                alt="{{ .Text }}" {{ with .Title}} title="{{ . }}" {{ end }} />
        {{- end }}
        ```
        
        创建指定位置的 extend_head.html 文件
        
        ```vim
        site_source/
        | archetypes/
        | assets/
        | config/
        | content/
        | data/
        | layouts/
        | | _default/
        | | partials/
        | | | comments.html
        | | | extend_head.html
        | public/
        | resources/
        | static/
        | themes/
        ```
        
        extend_head.html 文件里面写入
        
        ```html
        <style>
        @media screen and (min-width: 1px) {
            /* .post-content is a class which will be present only on single pages
                and not lists and section pages in Hugo */
            .post-content input[type="checkbox"]:checked ~ label > img {
                transform: scale(1.6);
                cursor: zoom-out;
                position: relative;
                z-index: 999;
            }
        
            .post-content img.zoomCheck {
                transition: transform 0.15s ease;
                z-index: 999;
                cursor: zoom-in;
            }
        }
        </style>
        ```
        

    - 其他的关于代码块语法高亮，搜索界面，归档界面都可以在 PaperMod 的 Wiki 文档里面找到答案
    
        👉 [Wiki](https://github.com/adityatelange/hugo-PaperMod/wiki)

2. Hugo 与 Hexo 的使用区别

    - 文章头部如果使用了 draft:true ，那么这篇文件将不会被生成，只是作为草稿    
    - hugo server 等价于 hexo g && hexo s ，但是不会将生成的文件存入磁盘中，也就是说，public 文件夹里面将不会有任何改变
    
    - hexo d 就可以发布了，不过在 hugo 里面要先执行 `hugo` ，在 public 下生成 html 文件，然后在 pulbic 下 git push 发布
