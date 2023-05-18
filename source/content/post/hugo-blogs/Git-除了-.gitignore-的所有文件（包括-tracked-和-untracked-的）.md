---
title: "Git 列出除了 .gitignore 以外的所有文件（包括 tracked 和 untracked 的）"
date: 2023-05-18T11:14:10+08:00
draft: false
tags:
    - Git
    - Markdown
---

在执行 `git ls-files` 的时候，只会列出被 git 跟踪了 (tracked) 的文件，而不会列出那些新建未跟踪的文件 (untracked) ，这个时候用以下命令就可以列出除了 .gitignore 以外的所有文件（包括 tracked 和 untracked 的）：

```bash
git ls-files --exclude-standard --cache --others
```

这个命令也可以简写成：

```bash
git ls-files --exclude-standard -c -o
```

或者：

```bash
git ls-files --exclude-standard -co
```

各项参数的意思（参考 Git 官方文档）：

> **-c** </br>
> **--cached** </br> 
> Show all files cached in Git’s index, i.e. all tracked files. (This is the default if no -c/-s/-d/-o/-u/-k/-m/--resolve-undo options are specified.)

> **-o** </br>
> **--others** </br>
> Show other (i.e. untracked) files in the output

> **--exclude-standard** </br>
> Add the standard Git exclusions: .git/info/exclude, .gitignore in each directory, and the user’s global exclusion file.


**References 👇**

[官方文档 git ls-files](https://git-scm.com/docs/git-ls-files)

[关于这个问题的讨论](https://superuser.com/questions/1629799/listing-all-versioned-files-and-untracked-files-in-git)

---

顺便提一句，在写 Markdown 的引用（quote）的时候，没错就是在写这篇文章的时候

1. 第一，我发现在引用里面是可以使用 \*\***font**\*\* 来加粗字体的

2. 第二，是如果想要在引用（quote）里面换行，得显式地在行后面加上 "\</br\>" 才可以
