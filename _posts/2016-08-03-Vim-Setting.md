---
layout: post
title: "VIM 설정"
comments: true
description: ""
keywords: ""
---

## VIM 설정
1. 터미널 실행
2. vi ~/.vimrc 입력
3. 수정

<div class='divider'></div>

## 설정 내용

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

<div class='divider'></div>
