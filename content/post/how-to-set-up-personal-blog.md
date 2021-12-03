---
author: "cyx20080216"
title: "如何利用Hugo,Fuse,Gitalk,Github Pages,Github Actions搭建一个支持Marldown语法,搜索,评论,自动化部署,在线编辑的个人博客"
date: 2021-12-03T12:55:30+08:00
lastmod: 2021-12-03T12:55:30+08:00
tags: ["Hugo","Github","网站搭建"]
categories: ["网站搭建"]
---
~~哪个男孩不渴望一个拥有一个个人博客呢~~

本文将会一步一步地介绍个人博客的搭建的全过程（也是[我的个人博客](https://cyx20080216.github.io/blog/)的搭建过程）
注：如果有不明白的地方，可以看[我的个人博客的Github仓库](https://github.com/cyx20080216/blog)
# Hugo
Hugo是一个静态网站生成器。当然，它也可以生成我们要搭建的个人博客。
### 安装
这一步十分简单，只需要前往[Hugo在Github上的Release页面](https://github.com/gohugoio/hugo/releases/)，下载对应的版本并解压到合适的位置就行了。

你也可以下载Deb包，再通过`dpkg -i <.deb文件名>`安装

如非必需，建议下载extended版本，因为在之后可能会用到
### 使用
首先，选定一个目录来存放你的博客，你需要作如下事情：
- 在该目录下创建config.toml文件，用于存储配置信息（为TOML格式）
- 在该目录下创建themes文件夹，用于存储主题
- 在该目录下创建content文件夹，用于存储文章

你需要找一个Hugo主题，并把它放在themes文件夹下（我使用了[hugo-theme-even](https://github.com/olOwOlo/hugo-theme-even)）

依照你所使用的主题的文档或示例，编写config.toml和在content文件夹下添加文章（有时可能还要编辑themes/主题名/config.toml）

[我的config.toml](https://github.com/cyx20080216/blog/blob/master/config.toml)

一切就绪后，你可以在目录下运行`hugo server`来让Hugo构建网站，并可以通过[](http://localhost:1313)访问

注1：要构建设置了`draft: true`的草稿文章，请使用`hugo server --draft`

注2：命令中的`hugo`视可执行文件位置而定，除非你使用Deb包，下文也依旧如此
### 发布
测试完成后，你可以使用`hugo`来发布。此时构建结果会输出到public文件夹

然后，你可以利用Web服务器和浏览器，来访问它们。

注：如果你真的要就这样把它发布出去，你必须在config.toml中加入baseURL设置，值为今后访问它时使用的URL，如`https://cyx20080216.github.io/blog/`
# 部署在Github Pages上
在此之前，你需要
- 一个Github帐号
- 安装Git
- 知晓Git与Github的基本使用方法

以上不属于本文的主要内容，不再赘述

### 创建一个用于存放个人博客的仓库
建议仓库名为<用户名>.github.io

其余略
### 将个人博客推送至上一步创建的仓库
1. 进行前文所述的发布操作
2. 进入public目录
3. 执行`git init`
4. 执行`git remote add origin <创建的仓库的地址，如https://github.com/cyx20080216/blog.git>`
5. 执行`git add -A`
6. 执行`git commit -m "<提交描述>"`
7. 执行`git push`（此步骤可能要求你填写帐号和私人访问密钥）

其中3,4仅第一次推送时需执行

每一次博客内容的改变，都需要执行以上操作
### 配置Github Pages
前往`https://github.com/<Github用户名>/<创建的仓库名>/settings/pages`

设置Source部分的分支（此时应该只有一个，选择即可）和目录，点击Save

此时，你的博客访问地址将会在上方显示（用这个来配置之前说的`baseURL`）

至此，你的个人博客已基本完成。如果你满足于此，就可以关闭这个页面了，否则，请继续往下看

（未完待续）
