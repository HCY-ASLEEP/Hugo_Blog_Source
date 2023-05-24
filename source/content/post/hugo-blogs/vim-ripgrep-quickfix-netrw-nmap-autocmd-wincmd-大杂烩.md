---
title: "Vim ripgrep quickfix netrw nmap autocmd wincmd 。。。大杂烩？！"
date: 2023-05-24T13:51:22+08:00
tags:
    - VIM
---

啊，这篇文章就随意一点吧，就意识流地写吧（虽然一直都是这样子），记录一下最近搞 Neovim 的心得

### 1. 使用 nvim + coc 如何滚动悬浮窗口内的内容呢？

使用 nvim + coc 编写代码的时候，弹出来的悬浮窗口内有滚动条，我怎样才能滚动悬浮窗口内的内容呢？试试 ctrl-w ctrl-w 切换到 floatwin 中，再翻页；更顺手一些的操作是：如果触发 floatwin 的按键是 nmap K, 连按两次 K 就会切换到 floatwin 中

👉 [https://segmentfault.com/q/1010000040349668](https://segmentfault.com/q/1010000040349668)

</br>
    
### 2. Vim f和t的快捷方式

**[ 移动到指定字符 ]**

- 如果我们想在当前行内快速移动，可以使用f, t, F, T命令

- "f"命令移动到光标右边的指定字符上，例如，"fx"，会把移动到光标右边的第一个'x'字符上，"F"命令则反方向查找，也就是移动到光标左边的指定字符上

- "t"命令和"f"命令的区别在于，它移动到光标右边的指定字符之前，例如，"tx"会移动到光标右边第一个'x'字符的前面，"T"命令是"t"命令的反向版本，它移动到光标左边的指定字符之后

- 这四个命令只在当前行中移动光标，光标不会跨越回车换行符

- 可以在命令前面使用数字，表示倍数，例如，"3fx"表示移动到光标右边的第3个'x'字符上

- ";"命令重复前一次输入的f, t, F, T命令，而","命令会反方向重复前一次输入的f, t, F, T命令，这两个命令前也可以使用数字来表示倍数

👉 [https://www.jianshu.com/p/a46a89b460a9](https://www.jianshu.com/p/a46a89b460a9)

</br>
    
### 3. 如何在 Vim 里面使用 ripgrep ？
    
关于这个问题有两种解决思路：

1. **使用 Vim 里面自带的 quickfix**

    - 将 rg 搜索结果切割填充到 quickfix 里面，这个部分由 Vim 自己完成，要想使用这个功能，只需要在你的 vimrc 或者 init.vim 里面添加这句话就行
    
        ```vim
        set grepprg=rg\ --vimgrep\ --no-heading\ --smart-case
        ```

        👉 [https://github.com/BurntSushi/ripgrep/issues/425](https://github.com/BurntSushi/ripgrep/issues/425)
        
        👉 [https://www.reddit.com/r/vim/comments/1rzvsm/do_any_of_you_redirect_results_of_i_to_the/](https://www.reddit.com/r/vim/comments/1rzvsm/do_any_of_you_redirect_results_of_i_to_the/)
    
    - 但是这里有一个问题，就是虽然 rg 搜索速度是非常快的，但是将 rg 搜索结果重定向到 quickfix 里面是非常慢的，应为填充 quickfix 的时候，vimscript 会把每一行切割成一段一段的字典数据，以方便文字高亮着色还有后续跳转，在 Vim 普通模式下 :h cexpr 就可以知道原理了
    
        👉 [https://www.reddit.com/r/vim/comments/e6xw03/ripgrep_is_slow_when_using_grepprg_inside_vim_but/](https://www.reddit.com/r/vim/comments/e6xw03/ripgrep_is_slow_when_using_grepprg_inside_vim_but/)

</br>
    
2. **狂热 VIM 爱好者 -> 手搓一个 quickfix**

    既然填充的时候切割花费了大量时间，那么我再跳转的时候再切割获得跳转位置信息不久快啦？毕竟切割一行内容多慢都不会慢到哪里去
    
    - Vim 获取外部 shell 命令输出结果的方法：
    
        ```vim
        redir => t:message
        execute a:cmd
        redir END
        echo t:message
        ```

    - Vim 将 t:message 的信息填充进 buffer 里面可以使用 setline() 函数
    
    - 或者可以直接获取结果然后填充一步到位：
    
        ```vim
        read !ls ~
        ```
        
        👉 [https://vim.fandom.com/wiki/Capture_ex_command_output](https://vim.fandom.com/wiki/Capture_ex_command_output)
    
    - Vim 在不切换到另外一个窗口下操控另外一个窗口，可以使用 win_execute() 函数，Neovim 也是支持的（nvim 0.7.2 测试可以），用这个函数可以实现 quickfix 里 cNext 那种效果（这里提一嘴，win_execute 里面的 winid 是要通过 win_getid 获取的）
    
        ```vim
        let l:findWinId=win_getid(l:findWinNum)
        ```
    
        👉 [https://vi.stackexchange.com/questions/23271/set-option-on-window-without-going-to-the-window-and-come-back](https://vi.stackexchange.com/questions/23271/set-option-on-window-without-going-to-the-window-and-come-back)
        
        👉 [https://vi.stackexchange.com/questions/26602/how-to-scroll-in-another-window-without-switching-to-it](https://vi.stackexchange.com/questions/26602/how-to-scroll-in-another-window-without-switching-to-it)
    
    - Vim 在 ex （命令模式里面跳转到具体的某行某列）需要使用 cal 命令，如跳转到第 30 行的第 5 列：
    
        ```vim
        cal cursor(30, 5)
        ```
        
        👉 [https://stackoverflow.com/questions/11767956/how-do-i-move-the-cursor-to-a-specific-row-and-column](https://stackoverflow.com/questions/11767956/how-do-i-move-the-cursor-to-a-specific-row-and-column)    
    
    - Vim 允许设置 buffer 的文件类型（filetype）：
        
        ```vim
        setlocal filetype=your_type(不用双引号)
        ```
        
    - 打印 buffer 文件类型：
    
        ```vim
        echo &filetype
        ```
        
    - 这个 filetype 属性结合 nnoremap \<buffer\> 就可以做到指定某一个 buffer setlocal cursorline (Buffer local mappings in Vim) (vim map in specific window)
    
        👉 [https://stackoverflow.com/questions/7985813/buffer-local-mappings-in-vim-buffer-vs-localleader](https://stackoverflow.com/questions/7985813/buffer-local-mappings-in-vim-buffer-vs-localleader)
    
    - 我将 rg 整合进 neovim 之后的代码如下：
    
        ```vim
        " set ripgrep root dir
        let g:rgRootDir=getcwd()
        
        function! CdCurBufDir()
            let g:rgRootDir=expand("%:p:h")
            echo expand("%:p:h")
        endfunction
        
        " Cc means 'cd cur', cd cur buf dir
        command! -nargs=1 -complete=command Cc silent call CdCurBufDir()
        
        let t:redirPreviewWinnr = 1
        
        function! OpenRedirWindow()
            let l:findWinNum=bufwinnr(bufnr('FuzzyFilenameSearch'))
            let l:rgWinNum=bufwinnr(bufnr('RipgrepWordSearch'))
            if l:findWinNum != -1
                exec l:findWinNum."wincmd w"
                enew
            elseif l:rgWinNum != -1
                exec l:rgWinNum."wincmd w"
                enew
            else
                let t:redirPreviewWinnr = winnr()
                botright 10new
            endif
        endfunction
        
        function! QuitRedirWindow()
            let l:findWinNum=bufwinnr(bufnr('FuzzyFilenameSearch'))
            let l:rgWinNum=bufwinnr(bufnr('RipgrepWordSearch'))
            if l:findWinNum != -1
                exec l:findWinNum."close"
            elseif l:rgWinNum != -1
                exec l:rgWinNum."close"
            else
                echo ">> No OpenRedirWindow!"
            endif
        endfunction
        
        nnoremap <silent><space>q :call QuitRedirWindow()<CR>
        
        " Fuzzy Match filenames -----------------------------------------------------------------------------
        " Go to the file on line
        function! FindJump(path)
            exec "cd ".g:rgRootDir
            let l:path=a:path
            exec t:redirPreviewWinnr."wincmd w"
            exec "edit ".l:path
        endfunction
        
        " autocmd to jump to file with CR only in FuzzyFilenameSearch buffer
        function! FindJumpWithCR()
            augroup findJumpWithCR
                autocmd!
                autocmd FileType FuzzyFilenameSearch nnoremap <buffer><silent><CR> :call FindJump(getline('.'))<CR>
            augroup END
        endfunction
        
        " redirect the command output to a buffer
        function! FindRedir(cmd)
            call FindJumpWithCR()
            call OpenRedirWindow()
            edit FuzzyFilenameSearch
            exec "read ".a:cmd
            setlocal buftype=nofile bufhidden=wipe nobuflisted noswapfile cursorline filetype=FuzzyFilenameSearch
        endfunction
        
        command! -nargs=1 -complete=command FindRedir silent call FindRedir(<q-args>)
        
        " Show Files fuzzily searched with git
        function! FindWithGit(substr)
            exec "FindRedir !rg --files \| rg --ignore-case ".a:substr
            exec "normal! gg"
            if getline('.') == ""
                exec "normal! dd"
            endif
            call feedkeys("/".a:substr."\\c\<CR>" ,'n')
        endfunction
        
        " Show Files searched fuzzily without git
        function! FindWithoutGit(substr)
            exec "FindRedir !rg --no-ignore --files \| rg --ignore-case ".a:substr
            exec "normal! gg"
            if getline('.') == ""
                exec "normal! dd"
            endif
            call feedkeys("/".a:substr."\\c\<CR>" ,'n')
        endfunction
        
        " Fg means 'file git', search file names fuzzily with git
        command! -nargs=1 -complete=command Fg silent call FindWithGit(<q-args>)
        
        " Fs means 'file search', search file names fuzzily
        command! -nargs=1 -complete=command Fs silent call FindWithoutGit(<q-args>)
        
        " To show file preview, underlying of FindNext, imitate 'cNext' command
        function! FindShow(direction)
            let l:findWinNum=bufwinnr(bufnr('FuzzyFilenameSearch'))
            if l:findWinNum == -1
                echo ">> No FuzzyFilenameSearch Buffer!"
            else
                if l:findWinNum != t:redirPreviewWinnr
                    let l:findWinId=win_getid(l:findWinNum)
                    call win_execute(l:findWinId, "normal! ".a:direction)
                    call win_execute(l:findWinId, "let t:findPreviewPath=getline('.')")
                    call FindJump(t:findPreviewPath)
                else
                    call FindJump(getline('.'))
                endif
            endif
        endfunction
        
        " imitate 'cNext'
        function! FindNext()
            call FindShow("+")
        endfunction
        
        " imitate 'cprevious'
        function! FindPre()
            call FindShow("-")
        endfunction
        
        nnoremap <silent><C-down> :call FindNext()<CR>
        nnoremap <silent><C-up> :call FindPre()<CR>
        
        
        " Global Fuzzy Match words -------------------------------------------------------------------------
        " Go to the file on line
        function! RgJump(location)
            exec "cd ".g:rgRootDir
            let l:location = split(a:location, ":")
            exec t:redirPreviewWinnr."wincmd w"
            exec "edit ".l:location[0]
            cal cursor(l:location[1], l:location[2])
        endfunction
        
        " autocmd to jump to file with CR only in RipgrepWordSearch buffer
        function! RgJumpWithCR()
            augroup rgJumpWithCR
                autocmd!
                autocmd FileType RipgrepWordSearch nnoremap <buffer><silent><CR> :call RgJump(getline('.'))<CR>
            augroup END
        endfunction
        
        " redirect the command output to a buffer
        function! RgRedir(cmd)
            call RgJumpWithCR()
            call OpenRedirWindow()
            edit RipgrepWordSearch
            exec "read "a:cmd
            setlocal buftype=nofile bufhidden=wipe nobuflisted noswapfile cursorline filetype=RipgrepWordSearch
        endfunction
        
        command! -nargs=1 -complete=command RgRedir silent call RgRedir(<q-args>)
        
        " Show Words fuzzily searched with git
        function! RgWithGit(substr)
            exec "RgRedir !rg '".a:substr."' ".getcwd()." --ignore-case --vimgrep --no-heading"
            exec "normal! gg"
            if getline('.') == ""
                exec "normal! dd"
            endif
            call feedkeys("/".a:substr."\\c\<CR>" ,'n')
        endfunction
        
        " Show Files fuzzily searched without git
        function! RgWithoutGit(substr)
            exec "RgRedir !rg '".a:substr."' ".getcwd()." --ignore-case --vimgrep --no-heading --no-ignore"
            exec "normal! gg"
            if getline('.') == ""
                exec "normal! dd"
            endif
            call feedkeys("/".a:substr."\\c\<CR>" ,'n')
        endfunction
        
        " Wg means 'word git', search file fuzzily names with git
        command! -nargs=1 -complete=command Wg silent call RgWithGit(<q-args>)
        
        " Ws means 'word search', search file fuzzily names without git
        command! -nargs=1 -complete=command Ws silent! call RgWithoutGit(<q-args>)
        
        " To show file preview, underlying of RgNext, imitate 'cNext' command
        function! RgShow(direction)
            let l:rgWinNum=bufwinnr(bufnr('RipgrepWordSearch'))
            if l:rgWinNum == -1
                echo ">> No RipgrepWordSearch Buffer!"
            else
                if l:rgWinNum != t:redirPreviewWinnr
                    let l:rgWinId=win_getid(l:rgWinNum)
                    call win_execute(l:rgWinId, "normal! ".a:direction)
                    call win_execute(l:rgWinId, "let t:rgPreviewLocation=getline('.')")
                    call RgJump(t:rgPreviewLocation)
                else
                    call RgJump(getline('.'))
                endif
            endif
        endfunction
        
        " imitate 'cNext'
        function! RgNext()
            call RgShow("+")
        endfunction
        
        " imitate 'cprevious'
        function! RgPre()
            call RgShow("-")
        endfunction
        
        nnoremap <silent><S-down> :call RgNext()<CR>
        nnoremap <silent><S-up> :call RgPre()<CR>
        ```
        
        👉 [https://github.com/HCY-ASLEEP/NVIM-Config/tree/main](https://github.com/HCY-ASLEEP/NVIM-Config/tree/main)
    
    
</br>

### 4. Ripgrep 在线使用手册

- 一些简单的例子说明：
    
    👉 [https://jdhao.github.io/2020/02/16/ripgrep_cheat_sheet/](https://jdhao.github.io/2020/02/16/ripgrep_cheat_sheet/)
    
- rg 在线 man 手册，没有例子：
    
    👉 [https://www.mankier.com/1/rg](https://www.mankier.com/1/rg)
    
- 顺便提一下防止我以后忘记，如果想 rg 搜索结果里面包含隐藏文件（以 '.' 开头的文件），使用 --hidden 参数就可以了
    
</br>

### 5. Where is neovim's .viminfo located?

vim 的历史命令文件位于 ~/.viminfo ，而 neovim 的位于 

```bash
~/local/share/nvim/shada/main.shada：
```

👉 [https://www.reddit.com/r/neovim/comments/5con51/where_is_neovims_viminfo_located/](https://www.reddit.com/r/neovim/comments/5con51/where_is_neovims_viminfo_located/)

</br>

### 6. 设置 netrw 的工作目录(root dir)

How to set selected directory as current in vim (netrw)? 只需要把光标放在 netrw 里面你想设置的目录下面，然后执行 :Ntree 命令就行

👉 [https://stackoverflow.com/questions/33542513/how-to-set-selected-directory-as-current-in-vim-netrw](https://stackoverflow.com/questions/33542513/how-to-set-selected-directory-as-current-in-vim-netrw)
