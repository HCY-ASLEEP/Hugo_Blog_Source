---
title: "Git 修正中文乱码"
date: 2023-04-18T10:06:35+08:00
tags:
    - Git
---

在 Linux （Mac）使用 `git status` 或者 `git ls-files` 的时候，若 git 在显示中文文件名的时候出现类似下面这个乱码：

`
"\321\203\321\201\321\202\320\260\320\275\320\276\320\262"
`

这个因为 git 默认预设之会打印出 non-ASCII 字符，对于 UTF-8 的文件名或者信息，会用 `quoted octal notation` 印出。

> Git has always used octal utf8 display, and one way to show the actual name is by using printf in a bash shell.
> 
> Since Git 1.7.10 introduced the support of unicode, this wiki page mentions:
> 
> By default, git will print non-ASCII file names in quoted octal notation, i.e. “\nnn\nnn…”.

要使 git 能正常显示中文，使用一下命令

```bash
git config [--global] core.quotepath off
```

**Reference👇**

[http://stackoverflow.com/a/22828826/3744499](http://stackoverflow.com/a/22828826/3744499)

[https://leoluyi.tw/git/git-display-utf8](https://leoluyi.tw/git/git-display-utf8)
