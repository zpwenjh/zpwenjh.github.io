---
layout:     post
title:      如何使用Github搭建图床及博客系统？
subtitle:   Github+PicGo+Typora，你值得拥有
date:       2021-01-19
author:     wenjh
header-img: img/post-bg-os-metro.jpg
catalog:    false
tags:
    - 图床及博客系统
---

# 新建仓库

在github上新建仓库，这一步没有什么难度，注意需要创建公开仓库，如果是私有仓库，那么别人都没法看到你的图了。

# 创建token

> Token是服务端生成的一串字符串，以作客户端进行请求的一个令牌，客户端只需带上这个Token前来请求数据即可，无需带上用户名和密码。
>
> Token的目的是为了减轻服务器的压力，减少频繁的查询数据库，使服务器更加健壮。

* 选择```Settings - Develop settings - Personal access tokens```进入如下页面。

  ![image-20210119113945437](https://zpwenjh.github.io/img-post/image-20210119113945437.png)

* 点击```Generate new token```，勾选```repo```，然后点击```Generate token```，会生成一个token，这个token只显示一次，记得保存。

# 安装并设置PicGo

PicGo是一个开源软件，直接从Github下载对应操作系统下的安装包安装就好。

* 进行如下设置。

  ![image-20210119151815517](https://zpwenjh.github.io/img-post/image-20210119151815517.png)

  ```Token```即为上面生成的Token。

  ```自定义域名``` 的作用是，在图片上传后，PicGo 会按照【自定义域名+储存路径+上传的图片名】的方式生成访问链接，并放到粘贴板上。

* 接下来打开```PicGo设置 - 设置Server```，按如下进行配置。

  ![image-20210119152509048](https://zpwenjh.github.io/img-post/image-20210119152509048.png)

# 配置Typora

要求Typora的版本在0.9.84及以上。

* 打开Typora，选择```文件 - 偏好设置 - 图像```，进行如下设置

  ![image-20210119152639450](https://zpwenjh.github.io/img-post/image-20210119152639450.png)

* 点击```验证图片上传选项```，如果```success```返回为```true```，则上传成功，github仓库中可以看到两个Typora的图标。

* 现在在Typora中粘贴图片，会自动上传到Github图床，并且自动替换连接。

---

关于如何在github搭建个人博客网站，可以参考下面文章，我就不盗文了。

[在Github搭建免费个人博客入门篇](https://zhuanlan.zhihu.com/p/74778630)

[在Github搭建免费个人博客提高篇](https://zhuanlan.zhihu.com/p/111832962)

[在Github搭建免费个人博客进阶篇](https://zhuanlan.zhihu.com/p/111846728)

---

