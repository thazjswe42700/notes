---
title: "自建私有图片视频分享平台Share Me,Next.js开源替代Imgur,NAS一键部署"
date: 2025-10-16
description: "Share Me 是开源自托管图片视频分享平台，基于 Next.js 和 PocketBase 构建，提供拖拽上传、分享链接、社交预览等功能。支持用户认证、S3 存储、OIDC 登录，完美替代 Imgur。一键 Docker 部署，轻松掌控私密媒体资源。"
canonical: https://www.passnat.com/posts/self-hosted-private-image-video-sharing-platform-share-me-nextjs-open-source-imgur-alternative-nas-one-click-deploy
---

## 项目介绍

Share Me 是一个自托管的图片和视频上传分享平台，基于 Next.js 和 PocketBase 构建，是 Imgur 的开源替代方案。这个项目让你能够完全掌控自己的媒体资源，支持拖拽上传、粘贴上传、分享链接生成、内容标记等丰富功能。

项目采用现代化的技术栈，前端使用 Next.js 构建，后端基于 PocketBase 提供强大的数据管理能力。支持用户注册登录、OIDC 单点登录、邮件验证、S3 云存储配置等企业级特性。生成的分享链接支持自动生成 OpenGraph 和 Twitter 元数据，完美适配 WhatsApp、Discord、Twitter、Facebook 等社交平台的分享预览。

无论是个人使用还是团队部署，Share Me 都能为你提供一个私密、可靠、功能完整的媒体分享解决方案。

项目地址：[点我跳转](https://github.com/Dan6erbond/share-me)

## 部署安装

如果你需要在公网环境下访问 Share Me，建议配置内网穿透服务以便随时随地访问你的私有分享平台。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，完整的上手指南请 [点我查看](https://www.passnat.com/posts/p)。

### 快速部署

Share Me 提供了 Docker Compose 的开箱即用配置，让部署变得轻而易举。

**复制下列配置文件到你的 NAS 或服务器**

```yaml
version: '3.8'

services:
  server:
    image: ghcr.io/dan6erbond/share-me-server:0.6.0
    volumes:
      - ./pb_data:/pb/pb_data
    ports:
      - 8080:8080
  frontend:
    image: ghcr.io/dan6erbond/share-me-frontend:0.6.0
    environment:
      POCKETBASE_URL: http://localhost:8080
    ports:
      - 3000:3000
```

打开你的飞牛 NAS 或其他 NAS 设备的容器管理界面，开始部署吧～

**第一步：导入配置**

在 NAS 的容器管理中选择'从文件导入'或'Docker Compose'选项，将上面的配置文件粘贴进去。

**第二步：配置端口映射**

系统会自动读取配置中的端口设置：

PocketBase 后端服务映射到宿主机的 **8080 端口**

Next.js 前端应用映射到宿主机的 **3000 端口**

如果你的 NAS 上这些端口已被占用，可以修改配置文件中的前一个数字（宿主机端口）。例如想将前端改到 3001 端口，改为 3001:3000 即可，容器内部的端口保持不变。

**第三步：配置存储**

配置文件中已经设置了 ./pb\_data:/pb/pb\_data 的数据卷映射，用于持久化存储 PocketBase 的数据库和上传文件。系统会自动在当前目录创建 pb\_data 文件夹。如果你希望指定其他位置，修改 ./pb\_data 为你想要的路径即可。

**第四步：环境变量配置**

根据你的实际需求，可以添加以下环境变量到 frontend 服务中：

**必须设置**

POCKETBASE\_URL：PocketBase 服务的访问地址。本地部署填 http://localhost:8080；如果使用反向代理（如 Nginx、Traefik），填写完整的域名如 https://shareme.example.com

**可选设置**

SIGNUP\_ENABLED：是否允许用户注册（true/false，默认为 true）

UMAMI\_WEBSITE\_ID：Umami 分析工具的网站 ID（用于统计访问数据）

UMAMI\_JS：Umami 分析脚本地址

如果不需要这些高级功能，直接保持默认配置即可。

**第五步：启动服务**

点击'启动'或'部署'按钮，系统会自动拉取镜像并启动容器。首次启动可能需要几分钟时间下载镜像。

**第六步：访问应用**

部署成功后，在浏览器中访问 http://你的NAS地址:3000 即可打开 Share Me 应用。

首次访问会自动跳转到登录页面。你可以选择创建新账户进行注册，也可以使用已有账户登录。

### 高级配置

**S3 云存储配置**

如果你想将上传的文件存储到 AWS S3 或其他兼容 S3 的存储服务（如 MinIO、DigitalOcean Spaces），可以在 PocketBase 管理后台进行配置：

访问 http://你的NAS地址:8080 进入 PocketBase 管理界面 → 设置 → 文件存储 → 启用 S3 并填入相应的配置信息。

**修改文件大小限制**

默认单个文件大小限制为 5MB。如需调整，进入 PocketBase 管理界面 → 数据表 → files → 设置（齿轮图标）→ 编辑 file 字段 → 修改'最大文件大小'即可。

**邮件验证配置**

如需启用邮件验证功能，在 PocketBase 管理界面 → 设置 → 邮件设置中配置你的 SMTP 服务器信息。

**SSO 单点登录**

支持通过 OIDC 协议集成第三方认证服务。在 PocketBase 管理界面 → 设置 → 认证提供商中进行配置。

本篇教程结束。现在你已经拥有了自己的私有媒体分享平台，尽情享受吧！

---

原文：<https://www.passnat.com/posts/self-hosted-private-image-video-sharing-platform-share-me-nextjs-open-source-imgur-alternative-nas-one-click-deploy>
