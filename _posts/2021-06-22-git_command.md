---
layout:     post
title:      git命令
subtitle:   常用git命令
date:       2021-06-22
author:     wenjh
header-img: img/home-bg-geek.png
catalog:    false
tags:
    - 版本管理
---

## 升级git(ubuntu环境)

```sh
sudo apt update  # 更新源
sudo apt install software-properties-common # 安装 PPA 需要的依赖
sudo add-apt-repository ppa:git-core/ppa    # 向 PPA 中添加 git 的软件源
sudo apt-get update
sudo apt-get install git
```

## 找出错误提交（git bisect)

假设仓库经过了n个提交，结果发现程序出错，如何找到是哪次提交引入了错误，进而找到错误的更改，进行修复呢？

git为我们提供了git bisect命令，其原理是将历史提交，按照二分法不断缩小定位。具体操作如下：

* 首先确定没有问题的commit id，作为查错起点，最近一次的提交作为查错的终点。运行如下命令：

  ```sh
  git bisect start [终点] [起点]
  # 例如: 
  # git bisect start HEAD XXXX
  ```

  执行上面的命令，代码会切换带该范围正当中的那次提交。

* 确认该次提交是否正确，根据正确与否，输入如下命令：

  ```sh
  # 代表错误在后半段引入,代码自动切换到后半段的正当中的那次提交
  git bisect good
  
  # 代表错误在前半段引入,代码自动切换到前半段的正当中的那次提交
  git bisect bad
  ```

* 重复执行第二步，直至成功找到有问题的那次提交。git会给出如下提示：

  ```sh
  XXXX is the first bad commit
  ```

* 找到错误的提交后，检查代码，确认错误位置，如何修复，然后使用`git bisect reset`回到最近一次的代码提交：

  ```sh
  git bisect reset
  ```

* 最后在最近一次的代码提交基础上修复问题。

## 多个分支同时工作（git worktree）

有时候，需要同时维护多个分支，频繁切换分支会相当麻烦。为此，我们可能将一个代码仓库复制多份，放在不同的目录中，但是这样的话，git的metadata也会被存储多次，而且各个目录之间的分支无法实时共享，并且，远程仓库有更新的话，各个目录下的git仓库都必须单独逐个进行更新。

git为我们提供了`git worktree`命令解决上述问题。假设对于仓库XXX.git，位于目录XXX下，我们可以在XXX目录下，通过如下命令，在YYY目录下建立一个新的worktree：

```sh
git worktree add ../YYY
```

也可以基于某个分支在新的worktree建立新的分支：

```sh
git worktree add -b new-branch ../YYY base-branch
```

在新的YYY目录下的操作不会污染其他目录，同时在该worktree的提交会即时同步，对其他worktee可见。

