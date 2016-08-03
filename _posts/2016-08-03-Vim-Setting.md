---
layout: post
title: "VIM 플러그인 설정"
comments: true
description: ""
keywords: ""
---


## 순서

1. 터미널 실행
2. vi ~/.vimrc 입력
3. 수정

<div class='divider'></div>

## 플러그인 목록

1. vundle
2. NERDTree
3. vim-indent-guides


## ~/.vimrc 내용 

```
set nocompatible
filetype off

set rtp+=~/.vim/bundle/vundle/

call vundle#rc()
Bundle 'gmarik/vundle'
Bundle 'git://git.wincent.com/command-t.git'

Plugin 'The-NERD-tree'
Plugin 'nathanaelkane/vim-indent-guides'

call vundle#end()
filetype plugin indent on

nmap <F7> :NERDTreeToggle<CR>


set nu
set ai
set paste
syntax enable
set ts=4 sw=4 et
colorscheme tir_black



let g:indent_guides_enable_on_vim_startup =1
let g:indent_guides_auto_colors = 0

let g:indent_gudies_start_level =2
let g:indent_guides_guide_size=1

hi IndentGuidesOdd guibg=darkgrey ctermbg=236
hi IndentGuidesEven guibg=darkgrey ctermbg=239

```
