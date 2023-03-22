---
title: "VIM 全局搜索目录下所有文件关键词"
date: 2023-03-22T19:40:22+08:00
tags:
    - VIM
---

- vimgrep 和 lvimgrep 是 vim 内置的搜索命令，可以处理不太复杂的正则表达式

- 它们的搜索结果都会放入一个列表里面，grep 和 vimgrep 的搜索结果放在 quickfix list 里，quickfix list 可以使用 :cw 或者 :copen 在 vim 中打开，它的结果可以和所有的vim窗口共享；lgrep 和 lvimgrep 的结果存放在 location list 里，location list 是当前窗口的，可以使用 :lw 或者 :lopen 打开

- 最妙的是搜索结果在cw或lw展现的时候，可以回车跳转到指定文件的搜索文本的位置

- g：代表所有匹配，而不是其中的一行匹配 j：代表vim不会自动跳转到第一个匹配的地方

- 比如要在当前文件夹下递归所有文件搜索tar或者是zip，就可以这样搜：
    
    ```vim
    : lvim /\<\(tar\|zip\)\>/gj **/*
    : lw
    ```
    
- tip: 使用cword取当前文件光标所在出的文字，.vimrc配置如下：

    ```vim
    map <F3> :execute "lvimgrep /" . expand("<cword>") . "/gj **/*" <Bar> lw<CR>
    ```
    
- 上述配置完成后，在vim中当前光标下，按下F3就会在vim的当前目录下搜索所有的文件及其子文件夹的文件，并显示出来，还可以使用 %:e 来做，意思是当前目录（%）下的同类型文件（e），如下：
    
    ```vim
    map <F3> :execute "lvimgrep /" . expand("<cword>") . "/gj " . expand("%:e") <Bar> lw<CR>
    ```

- 关闭autocmds以加速搜索，使用vimgrep搜索上百个文件会很慢，而用外置的grep就很快，一个原因是vimgrep使用vim的时序来读取文件，而这个时序将执行几个autocommands，所以我们在检索时关掉这个功能就会提速不少，所以最终的vimrc中配置如下：

    ```vim
    map <F3> :noautocmd execute "lvimgrep /" . expand("<cword>") . "/gj **/*" <Bar> lw<CR>
    ```
