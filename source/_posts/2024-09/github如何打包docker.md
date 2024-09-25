---
title: 如何通过 github actions 打包 docker 镜像
date: 2024-09-26 05:42:30
categories:
    - 随便写点
tags:
    - github
    - docker
    - actions
    - github actions
---



在现代软件开发中，自动化是提高效率和减少人为错误的关键。GitHub Actions 是一个强大的工具，可以帮助开发者自动化各种任务，包括构建和部署 Docker 镜像。在这篇文章中，我们将介绍如何通过 GitHub Actions 自动化打包 Docker 镜像的过程。

## 前提条件

在开始之前，请确保你已经具备以下条件：

1. 一个 GitHub 账户
2. 一个 Docker Hub 账户
3. 一个包含 Dockerfile 的 GitHub 仓库

## 步骤一：创建 GitHub 仓库

首先，你需要在 GitHub 上创建一个新的仓库。如果你已经有一个包含 Dockerfile 的仓库，可以跳过这一步。

1. 登录 GitHub
2. 点击右上角的 “+” 按钮，选择 “New repository”
3. 输入仓库名称和描述，选择公开或私有，然后点击 “Create repository”

## 步骤二：编写 Dockerfile

确保你的仓库中包含一个有效的 Dockerfile。以下是一个简单的示例：

```dockerfile
# 使用官方的 Node.js 镜像作为基础镜像
FROM node:14

# 创建和设置工作目录
WORKDIR /app

# 复制 package.json 和 package-lock.json
COPY package*.json ./

# 安装依赖
RUN npm install

# 复制应用代码
COPY . .

# 暴露应用运行的端口
EXPOSE 3000

# 启动应用
CMD ["node", "app.js"]
```

## 步骤三：创建 GitHub Actions 工作流

在你的仓库中，创建一个 `.github/workflows` 目录，并在其中创建一个名为 `docker-image.yml` 的文件。以下是一个示例工作流文件：

```yaml
name: Build and Push Docker Image

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Log in to Docker Hub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v2
        with:
          push: true
          tags: ${{ secrets.DOCKER_USERNAME }}/my-app:latest
```

## 步骤四：配置 GitHub Secrets

为了安全地存储你的 Docker Hub 凭证，你需要在 GitHub 仓库中配置 Secrets。

1. 打开你的 GitHub 仓库
2. 点击 “Settings”
3. 在左侧菜单中选择 “Secrets and variables” -> “Actions”
4. 点击 “New repository secret”
5. 添加 `DOCKER_USERNAME` 和 `DOCKER_PASSWORD`，并分别输入你的 Docker Hub 用户名和密码

## 步骤五：推送代码并触发工作流

将你的代码推送到 GitHub 仓库的 `main` 分支。每次推送代码时，GitHub Actions 都会自动触发工作流，构建并推送 Docker 镜像到 Docker Hub。

```bash
git add .
git commit -m "Add GitHub Actions workflow for Docker"
git push origin main
```

## 结论

通过以上步骤，你已经成功配置了 GitHub Actions 来自动化构建和推送 Docker 镜像。这不仅提高了开发效率，还减少了手动操作的错误风险。希望这篇文章对你有所帮助，祝你在自动化之路上越走越远！