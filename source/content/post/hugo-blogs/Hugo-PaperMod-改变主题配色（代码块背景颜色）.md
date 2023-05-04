---
title: "Hugo PaperMod 改变主题配色（代码块背景颜色）"
date: 2023-05-04T15:47:32+08:00
tags:
    - Hugo
---

Hugo PaperMod 主题的 Code block 背景 (background) 颜色总是黑色，然后我使用 F12 检查之后发现，黑色的代码块背景源自一个 CSS 变量 “hljs-bg” ，然后我通过 Google 查到了如何修改这个变量的颜色值:

[https://github.com/adityatelange/hugo-PaperMod/discussions/645](https://github.com/adityatelange/hugo-PaperMod/discussions/645)

在网站（不是主题）中创建一个文件，例如 `assets/css/extended/theme-vars-override.css`

```
source
├──archetypes
├── assets
│   └── css
│       └── extended
│           ├── emacs.css
│           └── theme-vars-override.css
├── config
├── content
├── layouts
├── public
├── resources
└── themes
```

在 `theme-vars-override.css` 里面添加以下内容：

```css
:root {
    --theme: rgb(255, 255, 255);
    --entry: rgb(255, 255, 255);
    --primary: rgb(30, 30, 30);
    --secondary: rgb(108, 108, 108);
    --tertiary: rgb(214, 214, 214);
    --content: rgb(31, 31, 31);
    --hljs-bg: rgb(245, 246, 247);
    --code-bg: rgb(245, 246, 247);
    --border: rgb(238, 238, 238);
}
```

重新生成网站就行，喜欢什么配色可以自己微调

而代码块的代码高亮其实也是有着自己的主题的，因为我使用的代码块背景是 light 的，所以代码高亮的主题使用了 emacs 

首先得启用 chroma 颜色高亮，需要禁用 hljs :

[https://github.com/adityatelange/hugo-PaperMod/wiki/FAQs#using-hugos-syntax-highlighter-chroma](https://github.com/adityatelange/hugo-PaperMod/wiki/FAQs#using-hugos-syntax-highlighter-chroma)

然后在网站目录（不是主题目录）下生成对应的 chroma 主题文件：

```bash
hugo gen chromastyles --style=emacs > assets/css/extended/emacs.css
```

想要其他的 chroma 代码高亮主题可以在这个网站下面找：

[https://xyproto.github.io/splash/docs/all.html](https://xyproto.github.io/splash/docs/all.html)

但是 hugo 的这个 emacs 主题有点问题，里面某些代码的高亮是灰色的，在亮色的背景下面就会看不清，需要把这部分的代码改成黑色，这需要在上面生成的 emacs.css 里面修改这一行代码：

```css
/* CodeLine */ .chroma .cl {color:#333333; }
```

这样子就基本完成配置了
