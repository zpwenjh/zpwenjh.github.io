---
layout:     post
title:      非root权限下zsh的安装与配置
subtitle:   服务器终端也可以很美
date:       2021-03-09
author:     wenjh
header-img: img/post-bg-coffee.jpg
catalog:    false
tags:
    - 终端
---

# 前言
服务器操作系统是centos7.6，默认终端bash，非root权限，gcc，vim什么的都有。当前可以联网，但是由于墙的存在，无法从github下载。
本文目的是用zsh替换bash，并对zsh进行美化。

# 安装zsh
首先在本地下载好zsh源码，上传到服务器，解压，然后进入源码目录，使用如下命令:
```bash
./configure --prefix=~/software/installed/zsh/zsh-5.8 --disable-gdbm
make -j32 -lgdbm
make install
```
这里解释为什么使用`--disable-gdbm`，因为在不加该选项的时候，报了warning，看看服务器上的gdbm，也已经很老了，我就下载了最新的gdbm源码，并用和上述同样的方法编译了。然后export了环境变量。在make的时候手动加上`-lgdbm`。
# 配置环境
在`~/.bash_profile`最后中添加如下代码：
```bash
GDBM_DIR=$HOME/software/installed/gdbm/gdbm-1.18.1
ZSH_DIR=$HOME/software/installed/zsh/zsh-5.8
export PATH=$GDBM_DIR/bin:$ZSH_DIR/bin:$PATH
export LD_LIBRARY_PATH=$GDBM_DIR/lib:$LD_LIBRARY_PATH
# 下面这行作用是在每次登录终端时都使用zsh作为终端
# 相当于chsh -s ~/software/installed/zsh/zsh-5.8/bin/zsh #
#（因为没有root权限，所以没法这样改），
# 但是这样改要小心，否则容易无法登录服务器。注释掉该行，
# 在使用zsh时可以用exec zsh使用zsh替换当前的bash.
# [ -f $HOME/software/installed/zsh/zsh-5.8/bin/zsh ] && exec $HOME/software/installed/zsh/zsh-5.8/bin/zsh -1
```
# 安装oh-my-zsh
有了zsh，还得有oh-my-zsh，依照官网的安装命令，先下载install.sh到本地：
```bash
# 也就是去掉前面的sh -s
curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh
```
然后在github上将oh-my-zsh的master分支git clone到本地。
将install.sh和源码全部上传到服务器。
将源代码解压到.oh-my-zsh目录下。修改install.sh脚本，注释掉下载源码相关内容，修改后的部分如下：

```bash
if ! command_exists zsh; then
    echo "${YELLOW}Zsh is not installed.${RESET} Please install zsh first."
    exit 1
  fi

  #if [ -d "$ZSH" ]; then
  #  echo "${YELLOW}The \$ZSH folder already exists ($ZSH).${RESET}"
  #  if [ "$custom_zsh" = yes ]; then
  #    cat <<EOF

#You ran the installer with the \$ZSH setting or the \$ZSH variable is
#exported. You have 3 options:

#1. Unset the ZSH variable when calling the installer:
#   $(fmt_code "ZSH= sh install.sh")
#2. Install Oh My Zsh to a directory that doesn't exist yet:
#  $(fmt_code "ZSH=path/to/new/ohmyzsh/folder sh install.sh")
#3. (Caution) If the folder doesn't contain important information,
#   you can just remove it with $(fmt_code "rm -r $ZSH")

#EOF
#    else
#      echo "You'll need to remove it if you want to reinstall."
#    fi
#    exit 1
#  fi

 # setup_ohmyzsh
  setup_zshrc
```
最后运行./install.sh脚本，完成oh-my-zsh配置。
# 配置oh-my-zsh
我想要的主题是powerlevel10k，这是一个第三方主题，因此，需要下载，然后上传到服务器的~/.oh-my-zsh/customs/custom/thems/目录下。
在本地下载好zsh-syntax-highlighting和zsh-autosuggestions插件。上传到服务器的`~/.oh-my-zsh/custom/plugins`目录下，然后修改`~/.zshrc`，在plugins栏目下加入相关插件，修改`ZSH_THEME`为想要的主题：

```bash
# Set name of the theme to load --- if set to "random", it will
# load a random theme each time oh-my-zsh is loaded, in which case,
# to know which specific one was loaded, run: echo $RANDOM_THEME
# See https://github.com/ohmyzsh/ohmyzsh/wiki/Themes
ZSH_THEME="powerlevel10k/powerlevel10k"
...
# Which plugins would you like to load?
# Standard plugins can be found in $ZSH/plugins/
# Custom plugins may be added to $ZSH_CUSTOM/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(
    git
    last-working-dir
    zsh-syntax-highlighting
    zsh-autosuggestions
)
```
# 配置字体
首先确保本地和服务器终端至少有一个允许设置字体。若两个都允许，下面的操作可以同时在本地和服务器端做。
在配置powerlevel10k之前，先确认终端字体是否符合：
先验证终端是否支持truecolor:

```bash
print -P '%F{#ff0000}red%f'
```
然后验证终端显示颜色是否至少是256色：
```bash
print $terminfo[colors]
```
我的显示是8，不是256，因此打开~/.zshrc，加入如下代码
```bash
export TERM=xterm-256color
```
执行`exec zsh`(如果配置的打开终端自动进入zsh，则直接重开一个终端即可)。
验证是否支持utf-8:

```bash
locale -a
```
现在终端长这样：

![20201216174555794](https://zpwenjh.github.io/img-post/20201216174555794.png)

可以看到，有些字符没有正常显示，该主题需要配置powerline font字体。在[powerlevel10k官放仓库](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k)下载下面四个字体。

![20201224174535591](https://zpwenjh.github.io/img-post/20201224174535591.png)

根据对应操作系统安装字体。
最后给终端设置字体，我的服务器端终端不支持设置字体，本地终端用的windows terminal，因此只设置本地终端字体，然后通过本地终端连接服务器即可显示特殊字符。

