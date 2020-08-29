---
---

Well... I tried to use emacs, but I found myself ending up wasting
time to configure everything from scratch and then retraining my
muscle memory to match the same speed as in vim (which is really slow
compared to other Vim experts :|). There were other emacs
distributions like doom emacs and spacemacs, but I didn't feel like
using them. Its better to just use plain Vim with a very minimal
config and Visual Studio code which now has Settings Sync support. The
only thing that kept me from using VS Code previously was the large
file support which is obviously needed for a file with more than 10k
lines. Here's the config I use for vim:

```vim
" Place in ~/.vimrc

set nocompatible

" Syntax highlighting
if has("syntax")
  syntax on
endif

set showcmd  " Show partial command in status line 
set ignorecase " Ignore case while searching
set showmatch  " Show matching bracket
set smartcase  " Do smartcase matching
set incsearch  " Incremental search
set autowrite  " Automatically save before executing make, etc.
set mouse=a  " Enable mouse interaction
set smartindent  " Smart indenting
set autoindent  " Automatically indent
set number  " Show line numbers
set relativenumber  " Show relative numbers
set encoding=UTF-8  " UTF-8 encoding for international characters
set scrolloff=5  " Minimum number of lines to show above and below the cursor
set wildmenu  " Auto-complete menu
set laststatus=2  " Always show status line
" set cursorline

" Tabs
set smarttab
set tabstop=4
set shiftwidth=4
set expandtab  " Expand tabs to spaces

" To use `ALT+{h,j,k,l}` to navigate windows from any mode:
:tnoremap <A-h> <C-\><C-N><C-w>h
:tnoremap <A-j> <C-\><C-N><C-w>j
:tnoremap <A-k> <C-\><C-N><C-w>k
:tnoremap <A-l> <C-\><C-N><C-w>l
:inoremap <A-h> <C-\><C-N><C-w>h
:inoremap <A-j> <C-\><C-N><C-w>j
:inoremap <A-k> <C-\><C-N><C-w>k
:inoremap <A-l> <C-\><C-N><C-w>l
:nnoremap <A-h> <C-w>h
:nnoremap <A-j> <C-w>j
:nnoremap <A-k> <C-w>k
:nnoremap <A-l> <C-w>l

" Specify a directory for plugins
" - For Neovim: stdpath('data') . '/plugged'
" - Avoid using standard Vim directory names like 'plugin'
call plug#begin('~/.vim/plugged')

" On-demand loading
Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }

" rust.vim
Plug 'rust-lang/rust.vim'

" ctrlp.vim - fuzzy file search
Plug 'ctrlpvim/ctrlp.vim'

" Tagbar
Plug 'majutsushi/tagbar'

" Initialize plugin system
call plug#end()

" Use Ctrl-P to start fuzzy file search
let g:ctrlp_map = '<c-p>'
let g:ctrlp_cmd = 'CtrlP'
```
