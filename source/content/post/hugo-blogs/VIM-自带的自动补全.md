---
title: "VIM 自带的自动补全"
date: 2023-03-19T19:55:18+08:00
tags:
    - VIM
---

VIM 的自带补全是根据上下文出现过的单词作为补全元的，将以下代码加入到你的 init.vim 里面就可以实现 Tab 触发补全以及连续使用 Tab 来进行切换选中补全

```vim
" Simple tab completion -----------------------------------------------------------------------------
" A simple tab completion, if you use the coc.nvim, you should remove this simple completion
inoremap <expr> <Tab> getline('.')[col('.')-2] !~ '^\s\?$' \|\| pumvisible()
      \ ? '<C-N>' : '<Tab>'
inoremap <expr> <S-Tab> pumvisible() \|\| getline('.')[col('.')-2] !~ '^\s\?$'
      \ ? '<C-P>' : '<Tab>'

augroup SimpleComplete
    autocmd CmdwinEnter * inoremap <expr> <buffer> <Tab>
          \ getline('.')[col('.')-2] !~ '^\s\?$' \|\| pumvisible()
          \ ? '<C-X><C-V>' : '<Tab>'
augroup END

" When you use konsole, you may need this
hi Pmenu ctermfg=yellow
hi PmenuSel ctermbg=darkgray ctermfg=white
```

不过值得注意的是，这个 VIM 设置片段不应该与其他那些自动补全插件一起使用，容易造成冲突，比如不能和 coc.nvim 一起使用
