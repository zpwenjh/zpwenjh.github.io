---
layout:     post
title:      Vim配置
subtitle:   自用Vim配置，需者自取
date:       2021-03-24
author:     wenjh
header-img: img/post-bg-debug.jpg
catalog:    false
tags:
    - 编辑工具
---

# Vim配置

可以通过配置文件修改vim配置，vim配置文件有两个：

* `/etc/vimrc`：系统范围的配置文件，修改的话会影响所有用户；
* `~/.vimrc`：每个用户自己的配置文件，可能不存在，不存在的时候自己创建就好。

如果考虑要不影响别人，或者没有root权限，就可以修改`~/.vimrc`来修改vim配置。

## 常用配置

```sh
" vim5及之后的版本支持语法高亮。
if has("syntax")
  syntax on
endif
" 设置tab键的宽度为4个空格
set shiftwidth=4
" 设置读取文件时读到的tab键，解释为4个空格
set tabstop=4
" 设置编辑动作(Backspace)或(Tab)时，对tab的解释为4个空格，4个空格会被当作一个tab删除
set softtabstop=4
" 上述变量只是对tab的解释，实际保存文件时，tab键还是会被保存为\t，而不是空格
" 将tab键转成空格，retab!将现有文件打开时也进行转化，这里保存的就不是tab了
set expandtab
:retab!
" 采用vim自己的命令，不于vi兼容
set nocompatible
" 不创建备份文件
" set nobackup
" 区分大小写
set infercase
" 换行时不把一个单词拆开，遇到指定符号（比如空格，连词和其他标点符号）才换行
set linebreak
" 左侧显示行号
set number
" 显示光标当前行
set cursorline
" 在状态栏显示光标当前位置，哪一行哪一列
set ruler
" 高亮显示搜索的匹配结果，输入结束时才显示
set hlsearch
" 设置vim帮助为中文，并使用编码格式utf-8
if version >= 603
  set helplang=cn
  set encoding=utf-8
endif
" 遇到括号时，自动高亮匹配的括号
set showmatch
" 命令模式下，显示输入的命令
set showcmd
" 自动缩进，按下回车键，下一行会自动跟上一行的缩进保持一致
set autoindent
" 智能缩进
set smartindent
" 复制粘贴时保留原有的缩进
set copyindent
" 将tab键和多余的空格显示出来
set list
set listchars=tab:>>,trail:.
" 在命令模式下，按下tab键自动补全命令
set wildmenu
set wildmode=longest,list,full
" insert模式下，退格键可以删除任何东西
set backspace=indent,eol,start
" 即使长文本发生换行时，也逐行移动光标
map j gj
map k gk
" 重新打开文件时，跳到上次的位置
au BufReadPost *
\ if line("'\"") > 1 && line("'\"") <= line("$") |
\ exe "normal! g'\"" |
\ endif
" 自动切换到当前文件目录
set autochdir
" 记录历史命令的条数
set history=1000
" 撤销的次数
set undolevels=1000
" 发生错误时，视觉提示，通常是屏幕闪烁
set visualbell
" 支持使用鼠标
set mouse=a
" 设置背景色
set background=dark
" 设置主题，非自带，需要另外下载
:colorscheme molokai
```

## 重配主题

* 下载molokai主题，从[github](https://github.com/tomasr/molokai)下载，从下载后的molokai文件夹中找到colors文件夹，在里面找到molokai.vim这个配色文件。
* 将文件放入对应文件夹，同样，可以放入系统路径下，放入`/usr/share/vim/vim81(81是版本号，根据自己的版本来)/colors/`目录下；如果只想自己用，可以放入`~/.vim/colors/`（不存在则创建）目录下。
* 按照上面最后的配置进行配置。