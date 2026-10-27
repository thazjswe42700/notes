---
title: "PicX免费开源图床工具，GitHub图片托管管理，在线即用无需安装，支持批量上传链接转换"
date: 2025-10-16
description: "PicX是基于GitHub API的免费开源图床工具，无需安装即用。支持拖拽上传、批量操作、多级目录管理、智能压缩和多种链接格式转换。内置图片处理工具箱，支持暗黑模式和PWA离线使用，是博主和内容创作者的理想选择，可轻松实现图片的安全存储和高效管理。"
canonical: https://www.passnat.com/posts/picx-free-open-source-image-hosting-github-batch-upload
---

## PicX - 免费开源的图床工具，让图片管理更简单

### 项目介绍

PicX 是一款基于 GitHub API 开发的轻量级图床工具，无需下载安装，在线即用。它提供了完整的图片上传托管、链接生成和图片处理工具箱功能。通过与 GitHub 深度整合，让你能够安全地存储和管理图片资源，同时支持多种图片链接格式转换，是博主和内容创作者的必备工具。

项目地址：[点我跳转](https://github.com/XPoet/picx)

### 核心功能特性

多种上传方式：支持拖拽、复制粘贴、文件选择等灵活的图片上传方式

批量操作：支持批量上传、批量删除、批量复制链接，提高工作效率

智能重命名：上传时可自动哈希化文件名或添加自定义前缀，确保文件名唯一

多级目录管理：创建和管理多层级目录结构，组织图片井井有条

灵活链接格式：内置 GitHub、GitHub Pages、jsDelivr、Statically 等多种链接规则，支持自定义规则

图片处理工具：内置图片压缩、Base64 转换、水印添加等实用工具

智能压缩：支持自动压缩算法，减少图片体积无损质量

暗黑模式：支持自动/手动切换主题，保护眼睛

PWA 支持：离线也能使用，多语言支持（简体中文、繁体中文、英文）

### 部署安装

若需在外网也能访问项目，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的配置步骤可以 [查看这篇教程](https://www.passnat.com/posts/p)，帮助你快速上手全平台内网穿透方案。

#### 使用 Docker 快速部署

打开你的 NAS 设备或服务器的 Docker 管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**。

在镜像地址栏填入：xpoet/picx:latest

等待镜像下载完成后，点击 **启动** 按钮启动容器。

进入容器配置页面，勾选 **开机自动启动** 以确保服务持久运行。

#### 端口配置说明

容器内部运行在 3000 端口，你可以将其映射到任意可用的外部端口。如果你的 NAS 已占用 3000 端口，可修改为其他端口（如 8080、8888 等），只需保证容器内端口始终为 3000 即可。

#### 存储配置

PicX 是一个前端应用，数据存储在 GitHub 仓库中，因此无需配置本地存储目录。

#### 环境变量配置

PicX 无需额外的环境变量配置，保持默认即可。所有配置都可在应用启动后通过 Web 界面进行。

启动完成后，在浏览器中访问 http://你的NAS地址:3000（将 3000 替换为你设置的外部端口），即可看到 PicX 的登录界面。

首次使用时，通过 GitHub OAuth 授权或填写 GitHub Token 进行登录，完成图床配置后即可开始使用。

### 使用建议

确保你有一个 GitHub 账号，并生成个人访问令牌（Personal Access Token）

为了获得最佳体验，建议在 GitHub 上创建一个专用仓库用于存储图片

定期检查 GitHub 仓库的存储空间，避免超出限额

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/picx-free-open-source-image-hosting-github-batch-upload>


## 相关文章

- [家常菜谱聚合平台来做菜，材料有限也能做美食，支持离线PWA，NAS一键Docker部署](home-cooking-recipes-pwa-nas-docker-deployment.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问](appsmith-nas-docker-compose-local-deployment-guide.md)
- [自托管私人相册HomeGallery，AI智能识别，NAS极简部署，内网穿透远程访问](home-gallery-private-photos-ai-recognition-nas-deployment-remote-access.md)
- [Cogito个人知识库系统自建指南，NAS极简部署智能笔记管理工具，支持内网穿透远程访问](1-seo-slug-aa-bb-cc2-cogitoseoseo-slug------slug.md)
