+++
title = '基于Hugo博客搭建'
date = 2023-12-22T17:36:29+08:00
draft = false
+++

- [Hugo](https://gohugo.io/)
- [PaperMod 主题](https://github.com/adityatelange/hugo-PaperMod/)
- [Github Action](https://github.com/features/actions)：CICD自动部署
- [Giscus](https://giscus.app/zh-CN)：评论系统

# 目录：
<!-- TOC -->

* [1、初始化博客](#1初始化博客)
* [2、部署到Github Page](#2部署到github-page)
    * [2.1 新建Github仓库](#21-新建github仓库)
    * [2.2 生成Token](#22-生成token)
    * [2.3 将Token添加到Github仓库的Secrets](#23-将token添加到github仓库的secrets)
    * [2.4 添加cicd文件到blog/github/workflows/cicd.yml](#24-添加cicd文件到bloggithubworkflowscicdyml)
    * [2.5 push到github](#25-push到github)
        * [此时浏览器输入 https://{username}.github.io, 可以看到你的网站了。](#此时浏览器输入-httpsusernamegithubio-可以看到你的网站了)
* [3、配置Giscus评论系统](#3配置giscus评论系统)
    * [3.1 在 hugo.toml 添加如下配置](#31-在-hugotoml-添加如下配置)
    * [3.2 Github仓库中开启Discussions](#32-github仓库中开启discussions)
    * [3.3 在Github安装Giscus App](#33-在github安装giscus-app)
    * [3.4 配置Giscus](#34-配置giscus)
        * [配置完之后，看到下面生成了一个配置脚本：](#配置完之后看到下面生成了一个配置脚本)
        * [复制上面生成的脚本，新建文件blog/layouts/partials/comments.html，并将脚本写入这个文件](#复制上面生成的脚本新建文件bloglayoutspartialscommentshtml并将脚本写入这个文件)
        * [此时Giscus已经配置完毕了，push完自动部署之后可以看到:](#此时giscus已经配置完毕了push完自动部署之后可以看到)

<!-- TOC -->

# 1、初始化博客

[Hugo安装](https://gohugo.io/installation/)

```shell
hugo new site blog
cd blog
git init

# 安装PaperMod主题
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
# 设置主题为PaperMod
echo "theme = 'PaperMod'" >> hugo.toml

# 新建内容
hugo new content posts/基于Hugo博客搭建.md

```

# 2、部署到Github Page

## 2.1 新建Github仓库

仓库名必须为：{username}.github.io, 且要设置为public

## 2.2 生成Token

![](/基于Hugo博客搭建/生成token.png)

## 2.3 将Token添加到Github仓库的Secrets

![](/基于Hugo博客搭建/添加secrets.png)

## 2.4 添加cicd文件到blog/github/workflows/cicd.yml

```yaml
name: Deploy blog

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    concurrency:
      group: ${{ github.workflow }}-${{ github.ref }}
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
      #          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.ACCESS_TOKEN }}
          publish_dir: ./public
```

## 2.5 push到github

可以看到action已经在运行了
![](/基于Hugo博客搭建/查看actions.png)

选择gh-pages作为内容分支
![](/基于Hugo博客搭建/选择branch.png)

### 此时浏览器输入 https://{username}.github.io, 可以看到你的网站了。

# 3、配置Giscus评论系统

## 3.1 在 hugo.toml 添加如下配置

```toml
[params]
comments = true
```

## 3.2 Github仓库中开启Discussions

![](/基于Hugo博客搭建/开启Discussions.png)

## 3.3 在Github安装Giscus App

[https://github.com/apps/giscus](https://github.com/apps/giscus)
![](/基于Hugo博客搭建/安装giscus.png)

选择指定仓库
![](/基于Hugo博客搭建/选择指定仓库.png)

## 3.4 配置[Giscus](https://giscus.app/zh-CN)

一般只需要配置这两个选项就好
![](/基于Hugo博客搭建/giscus配置1.png)

![](/基于Hugo博客搭建/giscus配置2.png)

#### 配置完之后，看到下面生成了一个配置脚本：

![](/基于Hugo博客搭建/giscus配置3.png)

#### 复制上面生成的脚本，新建文件blog/layouts/partials/comments.html，并将脚本写入这个文件

### 此时Giscus已经配置完毕了，push完自动部署之后可以看到:

![sss](/基于Hugo博客搭建/giscus配置4.png)