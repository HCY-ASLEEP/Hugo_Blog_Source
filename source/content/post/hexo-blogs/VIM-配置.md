---
title: VIM 配置
date: 2022-10-29 14:31:55
tags:
    - VIM
---

VIM 配置是会随着使用时间慢慢改进的，需要看最新最完整的需移步到 [我的 Github 仓库](https://github.com/HCY-ASLEEP/NVIM-Config)


### VIM 配置说明<!--more-->

1. 安装 neovim 可以直接从 github 下载 appimage

2. 配置 neovim 首次启动使用 :checkhealth 查看配置的路径等信息

3. coc.nvim 的 LSP 服务需要 npm （ node.js ）提供支持，npm 安装的时候可以从官方网站那里下载然后安装到指定 path，之后的 -g 安装 npm 包的时候包会安装在安装 npm 的目录下面

4. 注意这个 coc-setting.json 这个在安装了 coc.nvim 之后可以 :CocConfig 来进行编辑，json 里面这一句话一定要加上，解决了初次 tab 选中的问题

5. 配置文件 init.vim 位于 /home/asleep/.config/nvim 下，coc-settings.json 也是在这个路径下面

6. vimplug 管理插件，这里有点特殊，这里我将其他插件安装在与 vimplug 的 plug.vim 同一个目录下面，即  /home/asleep/.local/share/nvim/site/autoload

7. 安装各种插件之前，应该要了解各个插件的依赖

8. 至于 vimplug 的安装，只需要把 github 上面的那个 plug.vim 文件弄下来放在上面说的那个 path 下面就可以调用 :PlugInstall，不用整个仓库弄下来

9. 至于 coc 的插件，coc.nvim 是一个插件管理器，coc.nvim 管理的插件在 /home/asleep/.config/coc/extensions 下面

10. 综上所述，如果要迁移 nvim 及其配置，只需要复制

    ```bash
    /home/asleep/.config/coc/extensions   “coc-extensions”
    
    /home/asleep/.config/nvim   “coc-settings.json”  “init.vim“
    
    /home/asleep/.local/share/nvim/site/autoload   “plug.vim” “vim-plug extensions”
    ```

11. 这三个路径下的文件就可以完成迁移 

12. 每次修改 init.vim 之后，都要 :so % 即 :source init.vim 生效

### coc-setting.json

```json
{
    "suggest.noselect": true,
}
```

###### 

### init.vim 已启用的配置


```vim
" -------------------------------------------------------------------------------------------------------------
" ---------------------------------------------common-start----------------------------------------------------
" -------------------------------------------------------------------------------------------------------------

set number
set mouse=c
set tabstop=4
set autoindent
set backspace=indent,eol,start
set hlsearch
set clipboard+=unnamedplus
set foldmethod=syntax
set nofoldenable
" 自动同步
set autoread
set fillchars=eob:\ 

" Vim jump to the last position when reopening a file
if has("autocmd")
  au BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | exe "normal! g'\"" | endif
endif


function! ESC_IMAP()
    let l:frontChar = getline('.')[col('.') - 2]
    if l:frontChar == ";" 
        call feedkeys("\<BS>\<BS>\<ESC>", 'n')
    else
        call feedkeys("\<BS>\;", 'n')
    endif
endfunction
inoremap <expr> ; ESC_IMAP()

set timeoutlen=200

nnoremap ;; <ESC>
vnoremap ;; <ESC>
snoremap ;; <ESC>
xnoremap ;; <ESC>
cnoremap ;; <ESC>
onoremap ;; <ESC>

" exit windows
tnoremap ;; <C-\><C-n>

" switch windows
nnoremap <TAB> <C-w>w
nnoremap vv <C-v>

echo expand("%:p:h")

cnoreabbrev fd echo expand("%:p:h")
cnoreabbrev vst vs<ENTER>:term
cnoreabbrev spt sp<ENTER>:term


" -------------------------------------------------------------------------------------------------------------
"-----------------------------------------------common-end----------------------------------------------------
"-------------------------------------------------------------------------------------------------------------
"
"
"
"-------------------------------------------------------------------------------------------------------------
"--------------------------------------------vim-plug-start---------------------------------------------------
"-------------------------------------------------------------------------------------------------------------



call plug#begin('/home/asleep/.local/share/nvim/site/autoload')
Plug 'itchyny/lightline.vim'
Plug 'joshdick/onedark.vim'
Plug 'neoclide/coc.nvim', {'branch': 'release'}
Plug 'jiangmiao/auto-pairs'
Plug 'ms-jpq/chadtree', {'branch': 'chad', 'do': 'python3 -m chadtree deps'}
Plug 'sheerun/vim-polyglot'
Plug 'Yggdroot/LeaderF', { 'do': ':LeaderfInstallCExtension' }
Plug 'tpope/vim-fugitive'
Plug 'sbdchd/neoformat'
Plug 'iamcco/markdown-preview.nvim', { 'do': 'cd app && yarn install' }
call plug#end()

"有些插件需要安装 nerd fonts！
"nerd fonts 包括了 powerline fonts！
"建议安装 DejaVuSansMonoNerd！

"这个是 LeaderF 的设置
let g:Lf_WindowPosition = 'popup'
cnoreabbrev ff LeaderfFile


cnoreabbrev fm Neoformat

cnoreabbrev mt MarkdownPreviewToggle
let g:mkdp_theme = "light"

"这个是 chadtree 的设置
"明确指定绑定的键之后，就不会使用默认的键
let g:chadtree_settings = {
  \ 'keymap.change_focus_up': [".."],
  \ 'keymap.secondary': ["<2-leftmouse>"]
\}

nnoremap <F2> :CHADopen<CR>

let g:onedark_terminal_italics=1
autocmd ColorScheme * highlight Normal ctermbg=NONE guibg=NONE 
colorscheme onedark


let g:lightline = {
      \'colorscheme' : 'onedark',
      \ 'separator': { 'left': '', 'right': '' },
      \ 'subseparator': { 'left': '', 'right': '' },
      \ 'component': {
            \ 'lineinfo': ' %3l / %L : %-2v',
            \ }, 
      \ }


"-------------------------------------------------------------------------------------------------------------
"-----------------------------------------------vim-plug-end--------------------------------------------------
"-------------------------------------------------------------------------------------------------------------



"-------------------------------------------------------------------------------------------------------------
"------------------------------------------------coc-start----------------------------------------------------
"-------------------------------------------------------------------------------------------------------------

inoremap <silent><expr> <TAB> coc#pum#visible() ? coc#pum#next(1) :"\<Tab>" 

nnoremap gd <Plug>(coc-definition)
nnoremap gt <Plug>(coc-type-definition)
nnoremap gi <Plug>(coc-implementation)
nnoremap gr <Plug>(coc-references)

" Use K to show documentation in preview window.
function! ShowDocumentation()
  if CocAction('hasProvider', 'hover')
    call CocActionAsync('doHover')
  else
    call feedkeys('K', 'in')
  endif
endfunction
nnoremap <silent> K :call ShowDocumentation()<CR>

" Highlight the symbol and its references when holding the cursor.
autocmd CursorHold * silent call CocActionAsync('highlight')

" Symbol renaming.
nnoremap <space>r <Plug>(coc-rename)

" Show all diagnostics.
nnoremap <silent><nowait> <space>a  :<C-u>CocList diagnostics<cr>

" Manage extensions.
nnoremap <silent><nowait> <space>e  :<C-u>CocList extensions<cr>

" Find symbol of current document.
nnoremap <silent><nowait> <space>o  :<C-u>CocList outline<cr>


"-------------------------------------------------------------------------------------------------------------
"-------------------------------------------------coc-end-----------------------------------------------------
"-------------------------------------------------------------------------------------------------------------
```

###### 

### init.vim 未启用的配置

```vim
""-------------------------------------------------------------------------------------------------------------
""----------------------------------------------netrw_start----------------------------------------------------
""-------------------------------------------------------------------------------------------------------------



let g:netrw_banner = 0
let g:netrw_liststyle = 3
let g:netrw_browse_split = 4
let g:netrw_altv = 1
let g:netrw_winsize = 15

set autochdir

" Toggle Vexplore with <F2>
function! ToggleVExplorer()
    if exists("t:expl_buf_num")
        let expl_win_num = bufwinnr(t:expl_buf_num)
        let cur_win_num = winnr()

        if expl_win_num != -1
            while expl_win_num != cur_win_num
                exec "wincmd w"
                let cur_win_num = winnr()
            endwhile

            close
        endif

        unlet t:expl_buf_num
    else
         Vexplore
         let t:expl_buf_num = bufnr("%")
    endif
endfunction

map <F2> :call ToggleVExplorer()<CR>




"" 状态栏无插件设置
hi VertSplit ctermfg=NONE ctermbg=NONE cterm=NONE
set fillchars=vert:\ 
""set fillchars=vert:\│
set statusline=%*\ %.50F\               "显示文件名和文件路径
set statusline+=%=%y%m%r%h%w\ %*        "显示文件类型及文件状态
set statusline+=%{&ff}\[%{&fenc}]\ %*   "显示文件编码类型
set statusline+=%l/%L,%c\ %*            "显示光标所在行和列
set statusline+=%3p%%                   "显示光标前文本所占总文本的比例


hi Statusline ctermfg=NONE ctermbg=NONE cterm=bold 
hi StatuslineNC ctermfg=NONE ctermbg=NONE cterm=NONE





""-------------------------------------------------------------------------------------------------------------
""-----------------------------------------------netrw-end-----------------------------------------------------
""-------------------------------------------------------------------------------------------------------------
```






