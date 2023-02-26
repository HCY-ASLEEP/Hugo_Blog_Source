---
title: Git Multi End Sync
date: 2023-02-11 11:19:54
description: git 远端比本地领先的时候如何同步
tags:
    - Github
    - Git
---

##### 从远端仓库获取最新代码合并到本地分支里

在日常开发中，很有可能几个终端都在开发同一个代码仓分支，导致本地分支里的代码“落后于”远端分支里的，我们需要做的就是从远端仓库获取最新代码合并到本地分支里。

1. git pull

    - 获取最新代码到本地，并自动合并到当前分支，前提是已经 "git init"  
    - 首先我们用命令行  去查询当前代码仓的远端分支  
        ```bash
        git remote -v
        ``` 
    - 然后直接去拉取并合并最新的代码（因为是直接合并，无法提前处理冲突，不推荐） 
    - 即拉取远端origin/master分支并合并到当前分支
        ```bash
        git pull origin master
        ```
    - 即拉取远端origin/test分支并合并到当前分支
        ```bash
        git pull origin test
        ```

2. git fetch + merge （需要额外的本地分支）

    - 首先我们用命令行去查询当前代码仓的所有远端分支
        ```bash
        git remote -v
        ```
    - 然后用命令行获取最新代码到本地临时分支（自定义为tempBranch），获取到的远端分支为origin/dev
    
	    ```bash
	    git fetch origin dev:tempBranch
	    ```
    - 用命令行去查看本地tempBranch分支和当前分支的版本差异
        ```bash
        git diff tempBranch
        ```
    - 接着用命令行合并本地临时分支tempBranch到当前分支
        ```bash
        git merge tempBranch
        ``` 
    - 最后用命令行来删除该临时分支
        ```bash
        git branch -D tempBranch
        ```
    - 这种方式需要建立并删除这个额外的本地分支

3. git fetch + merge （不额外建立本地分支）

    - 首先我们用命令行去查询当前代码仓的所有远端分支
        ```bash
        git remote -v
        ```
    - 然后用命令行来获取远端的origin/dev分支的最新代码到本地（假设本地当前分支为dev）  
        ```bash
        git fetch origin dev
        ```
    - 接着用命令行去查看本地dev分支和当前分支的版本差异
        ```bash
        git log -p dev..origin/dev
        ```
    - 最后用命令行来合并远端分支origin/dev 到当前分支
        ```bash
        git merge origin/dev
        ```
    - 这种方式可以不用额外建立本地分支


4. git fetch + reset + pull

    - 放弃修改，强制覆盖本地代码
    
        ```bash
        git fetch --all
        git reset --hard origin/main    # origin 是远端的意思
        git pull
        ```
        
        
5. git push

    - 强制提交本地分支覆盖远程分支
    
        ```bash
        # git push origin --force <branch_name>
        git push --force origin main
        ```


