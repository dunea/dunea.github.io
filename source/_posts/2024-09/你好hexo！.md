---
title: 你好hexo！如何快捷的搭建一个Github Pages离线博客？
date: 2024-09-26 04:05:04
categories:
  - 随便写点
tags:
  - hexo
  - 博客
  - dunea
  - 陈序员
  - 陈序员的博客
  - dunea的博客
---

## 前言

我这个博客是基于hexo搭建的，hexo是一个基于Node.js的静态博客框架，可以方便的将Markdown文件转换为静态网页，并且支持多种主题和插件。

<!-- more -->

## 安装步骤

### 1. 安装Node.js（已安装就跳过）

首先，你需要安装Node.js，这是一个前提条件。你可以从[Node.js官网](https://nodejs.org/)下载并安装。

### 2. 安装Hexo

安装Hexo之前，你需要先安装Git。Git是一个分布式版本控制系统，可以方便的管理和同步代码。你可以从[Git官网](https://git-scm.com/)下载并安装。

安装完Git之后，你可以使用以下命令安装Hexo：

```bash
npm install -g hexo-cli
```

### 3. 创建博客

安装完Hexo之后，你可以使用以下命令创建一个新的博客：

```bash
hexo init <博客名称>
```

我这里直接运行 `hexo init dunea-blog` 提示 hexo 不是内部或外部命令，也不是可运行的程序或批处理文件。

所以我使用的是 `npx hexo init dunea-blog` 来创建博客。

如果你们的环境变量正确，就不需要使用 `npx` 来初始化博客。

### 4. 安装主题

Hexo支持多种主题，你可以从[Hexo官网](https://hexo.io/themes/)下载并安装。

安装完主题之后，你可以使用以下命令安装主题：

```bash
hexo theme <主题名称>
```

我没安装，使用的默认主题。

到这里你的博客就安装ok了，接下来上传到github上就可以了。

## 上传到github

### 1. 创建仓库

在github上创建一个仓库，仓库名必须为 `username.github.io` ，比如我的就是 `dunea.github.io` 。

### 2. 上传博客

我是使用vs code 上传的，直接将 `public` 文件夹上传到仓库的 `main` 分支。

下面是详细部署的 1-2 步骤就能完成部署了。祝你生活愉快。

[在 GitHub Pages 上部署 Hexo](https://hexo.io/zh-cn/docs/github-pages)

[将 Hexo 部署到 GitLab Pages](https://hexo.io/zh-cn/docs/gitlab-pages)

## 总结

通过以上步骤，你可以快速搭建一个基于Hexo的博客，并且可以方便的管理和同步代码。

## 参考资料

- [Hexo官网](https://hexo.io/)
- [Git官网](https://git-scm.com/)
