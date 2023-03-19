---
title: "Git 自动化提交脚本 (bash)"
date: 2023-03-19T20:53:05+08:00
tags:
    - Git
---

我自己的:

```bash
cur=`date +%Y-%m-%d\ \|\ %H-%M-%S`
git add -A
git commit -m "$cur"
unset cur
```

网上的:

```bash
#!/bin/bash
echo "================="
echo "auto git by JOEY"
echo "======= 🤪  ========="

echo -e  "
▶ \033[33;1mgit add .
\033[0m"
git add .

git status
echo -e "
▶ \033[33;1mcommit message:
\033[37;1m" 
read msg

echo -e "
▶ \033[33;1mgit commit -m '$msg'
\033[0m"
git commit -m "$msg"

echo -e "
▶ \033[33;1mgit push
"
echo -e "\033[37;1mstart pushing ...\033[0m
"
git push
echo -e "
\033[37;1mAll Done\033[0m"
```

