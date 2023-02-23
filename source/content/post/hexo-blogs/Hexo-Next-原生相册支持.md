---
title: Hexo Next 原生相册支持
date: 2023-02-20 13:13:22
description: Hexo Next Group Pictures (Photo Album)
tags:
    - Hexo
---

非常简单况且比我写的 CSS 好很多，我的那个 CSS 在 firefox 显示不正常，之前我找不到是因为我的搜索词语不对，我之前搜索使用的是 photo album ，而 Next 使用的相册关键词是 group pictures！

```markdown
{% grouppicture [your_picture_number]-[layout] %}
{% endgrouppicture %}
```

layout 的取值范围为 2～10

```markdown
{% grouppicture 3-3 %}
!["your_picture_description"](/images/next.svg)
!["your_picture_description"](/images/next.svg)
!["your_picture_description"](/images/next.svg)
{% endgrouppicture %}
```

更加详细的效果参考 **[这里](https://theme-next.js.org/docs/tag-plugins/group-pictures)**
