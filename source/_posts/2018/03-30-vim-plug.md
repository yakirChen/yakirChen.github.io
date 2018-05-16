---
title: Vim Plug 插件管理
date: 2018/03/30
tags: [Vim]
categories: [Tech]
toc: true
description: Vim Plug 插件管理
---

##  获取NVim安装包

*[Install From Source 在这里](https://github.com/neovim/neovim/wiki/Building-Neovim)*
```bash
curl -OL \
    https://github.com/neovim/neovim/releases/download/nightly/nvim-macos.tar.gz

tar zxvf nvim-macos.tar.gz -C ${SERVRES}
# Path 引用 NVim Home
```

## `Nvim` 配置

1. `vim` 默认配置路径 `~/.vim` 和 `~/.vimrc`
2. `nvim` 默认配置路径 `~/.local/share/nvim` 和 `~/.config/init.vim`

原本在用户目录下建了软链 `~/.vim` 和 `~/.vimrc` 分别指向 `/Volumes/sd/repos/vim` 目录

```bash
# `~/.vim`
ln -s /Volumes/sd/repos/vim ~/.vim
ln -s /Volumes/sd/repos/vim ~/.local/share/nvim 
ln -s /Volumes/sd/repos/vim ~/.config/nvim
# `~/.vimrc`
ln -s \
    ~/Library/Mobile Documents/com~apple~CloudDocs/Developer/macOS-libs/vimrc \
	~/.vim/init.vim
```

试想 `Nvim` 和 `Vim` 应该可以共用一个配置目录, 于是尝试了一把. 

在目录 `~/.config` 和 `~/.local/share` 中都建立了一个软链 `nvim` 指向  
`/Volumes/sd/repos/vim`  
和 `Vim` 共享配置

在 `~/.config` 目录中创建一个软链 `init.vim` 指向  
`~/Library/Mobile Documents/com~apple~CloudDocs/Developer/macOS-libs/vimrc`  
作为 `NVim` 的配置文件

## 插件管理

流行的 `Vim` 插件管理器有好几个, 最先用过的是 `Vundle` 安装插件的使用有点慢, 换成了 `Vim-plug`.


## `Vim-plug` 安装

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

执行 `vim +PlugInstall +qall` 或 `nvim +PlugInstall +qall` 完成插件配置

### `Vim-plug` 常用命令

|命令|介绍|
|:---|:---|
|PlugUpgrade| 升级 `Vim-plug` 自身|
|PlugStatus| 检查插件的状态|
|PlugUpdate|安装升级插件|
|PlugInstall|安装插件|

`Vim-plug` 更多命令参考

**附上 `vimrc`**

```vim                                                    
set nocompatible " 关闭vi兼容模式
set t_Co=256 " 开启终端256色 terminal Color
set showcmd
set foldenable
set foldmethod=manual
set cc=100
set foldmethod=marker
colorscheme gruvbox
" colorscheme duoduo
" colorscheme desert
set background=dark
set termguicolors 
" set background=light
set laststatus=2 " 总是显示状态栏
set ruler " 显示光标当前位置
set number " 开启行号显示
set cursorline " 高亮显示当前行/列
set cursorcolumn
set hlsearch " 高亮显示搜索结果
syntax enable " 开启语法高亮功能
syntax on " 允许用指定语法高亮配色方案替换默认方案 自适应不同语言的智能缩进
" filetype plugin on
" filetype indent on
set expandtab " 将制表符扩展为空格
set tabstop=4 " 设置编辑时制表符占用空格数
set shiftwidth=4 " 设置格式化时制表符占用空格数
set softtabstop=4 " 让 vim 把连续数量的空格视为一个制表符
" set splitbelow " Open new windows below the current window.
set splitright
" color
" color dracula
set showmatch "  括号成对匹配 简写为set sm
set modifiable
set wildmenu " 增加命令行自动补全操作
set history=9999

call plug#begin('~/.vim/plugged')

Plug 'junegunn/vim-easy-align'
Plug 'rust-lang/rust.vim'
Plug 'racer-rust/vim-racer'
Plug 'itchyny/lightline.vim'
Plug 'fholgado/minibufexpl.vim'
Plug 'Yggdroot/indentLine'
"Plug 'morhetz/gruvbox'
"Plug 'https://github.com/junegunn/vim-github-dashboard.git'
"Plug 'vim-airline/vim-airline'
"Plug 'vim-airline/vim-airline-themes'
Plug 'Yggdroot/indentLine'
Plug 'majutsushi/tagbar'
Plug 'ctrlpvim/ctrlp.vim'
Plug 'https://github.com/tommcdo/vim-lion.git'
Plug 'nathanaelkane/vim-indent-guides'
"Plug 'vim-syntastic/syntastic'
Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }
"Plug 'tpope/vim-fireplace', { 'for': 'clojure' }
"Plug 'rdnetto/YCM-Generator', { 'branch': 'stable' }
"Plug 'Valloric/YouCompleteMe', { 'do': './install.py' }
Plug 'fatih/vim-go', { 'tag': 'v1.17' }
Plug 'iamcco/mathjax-support-for-mkdp'
Plug 'iamcco/markdown-preview.vim'

call plug#end()

set hidden
let g:racer_cmd = '/Users/yakir/Developer/local/bin'
"let g:racer_experimental_completer = 1

" Plugin indentLine settings.
let g:indentLine_char = "┆"
let g:indentLine_enabled = 1
let g:autopep8_disable_show_diff=1

" Plugin airline settings
" let g:airline_powerline_fonts = 1
" let g:airline#extensions#tabline#enabled = 1
" let g:airline_theme="luna"

" Plugin YCM settings.
"set completeopt=longest,menu
"let g:ycm_min_num_of_chars_for_completion=2
"let g:ycm_cache_omnifunc=0
"let g:ycm_seed_identifiers_with_syntax=1
"let g:ycm_complete_in_comments = 1
"let g:ycm_complete_in_strings = 1
"let g:ycm_collect_identifiers_from_comments_and_strings = 0
"let g:ycm_global_ycm_extra_conf = '~/.ycm_extra_conf.py'
"let g:syntastic_check_on_open=1
"let g:syntastic_enable_signs=1
"let g:syntastic_cpp_check_header = 1
"let g:syntastic_cpp_remove_include_errors = 1

" theme
" let g:molokai_original = 1
" let g:rehash256 = 1
" let g:airline_theme='one'

" Plugin syntastic settings.
set statusline+=%#warningmsg#
set statusline+=%{SyntasticStatuslineFlag()}
set statusline+=%*

let g:syntastic_always_populate_loc_list = 1
let g:syntastic_auto_loc_list = 1
let g:syntastic_check_on_open = 0
let g:syntastic_check_on_wq = 0
let g:syntastic_mode_map = {'mode': 'passive', 'active_filetypes': [], 'passive_filetypes': []}
" Use pylint to check python files.
let g:syntastic_python_checkers = ['pylint']
" map <F5> :SyntasticToggleMode<CR> :SyntasticCheck<CR>

" Ignore warnings about newlines trailing.
let g:syntastic_quiet_messages = { 'regex': ['trailing-newlines', 'invalid-name',
    \'too-many-lines', 'too-many-instance-attributes', 'too-many-public-methods',
    \'too-many-locals', 'too-many-branches'] }

" Switch buffer
nmap <C-b>n :bnext<CR>
nmap <C-b>p :bprev<CR>

vmap <C-c> :w !pbcopy<CR><CR>
```

~~### YouCompleteMe~~ (试用用发现体验不是很好... 所以弃用)

```bash
cd ~/.vim/plugged/YouCompleteMe`
# 使用 macOS 自带 python
python ./install.py --all
# 还需要安装以下两个组件(不然使用Nvim会报出提示)
# `YouCompleteMe unavailable: requires Vim compiled with Python (2.7 or 3.4+) support.`
pip2 install --upgrade neovim
npm install -g neovim
```

**然后可以愉快地边玩耍边学(zhuang)习(bi)了 😜**

---


**Reference:**  
[NeoVim](https://github.com/neovim/neovim)  
[vim-plug](https://github.com/junegunn/vim-plug)  
[YouCompleteMe](https://github.com/Valloric/YouCompleteMe#mac-os-x)
