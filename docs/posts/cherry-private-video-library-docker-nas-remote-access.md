---
title: "Cherry私有视频库管理系统｜Docker快速部署｜NAS内网穿透远程访问影视库"
date: 2025-10-16
description: "Cherry私有视频库管理系统，基于TypeScript和Svelte开发的现代化视频管理解决方案。支持用户认证、视频上传、媒体库管理，采用前后端分离架构，Docker容器化部署，开箱即用。打造个人影视收藏库或家庭媒体中心的完美选择。"
canonical: https://www.passnat.com/posts/cherry-private-video-library-docker-nas-remote-access
---

## Cherry - 私有视频库管理系统

### 项目介绍

Cherry 是一款功能强大的私有视频库管理系统，采用现代化技术栈构建，基于 TypeScript 和 Svelte 开发。它为用户提供了一个完整的视频内容管理解决方案，支持用户认证、视频上传、媒体库管理等核心功能。无论你是想要建立个人影视收藏库，还是为家庭成员打造共享媒体中心，Cherry 都能完美胜任。

项目采用现代化的前后端分离架构，支持 Docker 容器化部署，让你可以轻松在任何地方搭建属于自己的视频管理平台。内置数据库支持，开箱即用，无需复杂配置。

项目地址：[点我跳转](https://github.com/haishanh/cherry)

### 部署安装

如果你想在外网也能访问这个项目，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作流程可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手指南。

#### 使用 Docker 快速部署

打开你的 NAS 设备或其他支持 Docker 的系统，进入本地镜像管理界面。

点击 **添加镜像** - **从 URL 添加**，输入以下镜像地址：

```
ghcr.io/haishanh/cherry:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

#### 端口映射设置

在容器配置界面，找到端口映射设置。Cherry 默认运行在容器内的 **8000** 端口，你可以将其映射到宿主机的任意端口（如 8000、3000 等）。如果遇到端口占用的情况，只需修改宿主机端口号即可，容器内部端口保持 8000 不变。

#### 存储配置

Cherry 需要一个持久化存储目录来保存数据库和配置文件。建议在 NAS 上创建一个文件夹（例如 `/mnt/cherry-data`），然后将其映射到容器内的 `/data` 目录。这样即使容器重启，你的所有数据都会被完整保留。

#### 环境变量配置

Cherry 支持以下环境变量配置：

必须设置：

PUID：运行用户的 UID（默认值：1001）

PGID：运行用户的 GID（默认值：1001）

PORT：应用运行端口（默认值：5173）

可选设置：

BODY\_SIZE\_LIMIT：请求体大小限制，用于上传文件（默认值：52428800，约 50MB）

NODE\_ENV：运行环境（默认值：production）

其他配置保持默认即可，无需额外修改。点击启动完成部署。

#### 访问应用

容器启动成功后，在浏览器中访问 `http://你的NAS地址:8000` 即可打开 Cherry 应用。首次访问时，你可能需要创建管理员账户来初始化系统。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/cherry-private-video-library-docker-nas-remote-access>


## 相关文章

- [本地密码生成器PSWD，隐私优先TypeScript构建，支持Docker和NAS快速部署，内网穿透远程访问](local-password-generator-pswd-privacy-first-typescript-docker-nas-deployment-intranet-penetration-remote-access.md)
- [Hexon Hexo博客在线管理系统，图形化界面轻松管理文章，Docker极简部署内网穿透远程访问](hexon-hexo-blog-management-system-graphic-interface-docker-deployment.md)
- [无代码网页数据提取Maxun自建指南，2分钟训练机器人自动爬取，内网穿透远程访问](no-code-web-scraping-maxun-guide-2-minute-robot-training-intranet-penetration-remote-access.md)
- [现代React推箱子游戏，500+谜题无限撤销，NAS极简部署内网穿透在线玩](modern-react-sokoban-game-500-puzzles-unlimited-undo-nas-simple-deployment-intranet-penetration-online-play.md)
- [浏览器PPT编辑神器PPTist自建部署，无公网内网穿透远程访问演示文稿](pptist-self-hosted-internal-network-remote-access.md)
