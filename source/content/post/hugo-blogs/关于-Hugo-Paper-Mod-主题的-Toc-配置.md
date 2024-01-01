---
title: "关于 Hugo Paper Mod 主题的 Toc 配置"
date: 2024-01-01T08:48:56Z
draft: false
---

Hugo 默认生成 Toc 的只是一二三级标题，如果像六级标题都可以生成 Toc ，可以这样子配置:
```yaml
markup:
  tableOfContents:
    startLevel: 1
    endLevel: 6
    ordered: false
```

参考文章：
```uri
https://atlex00.com/hugo/toc/#configure-toc
```

如果想让 Hugo 拥有侧边栏 Toc ，参考我 fork 的这个仓库：
```uri
https://github.com/HCY-ASLEEP/hugo-PaperMod-Mod-Sidebar-Toc
```  

---

顺便说一句题外话，就是 Hugo 的各个文件夹都有着不同的用处，值得去了解一下，比如说 archetypes 文件夹用于存放 hugo new 的生成模板，而 assets 文件夹用于存放自定义的 override 的 css ，layouts 文件夹用于存放自定义的 js 或者 html
