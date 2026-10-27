---
title: "自托管视频分享平台 Fireshare 部署指南，NAS Docker 一键启动分享游戏录屏"
date: 2025-10-16
description: "Fireshare 是自托管视频分享平台，专为游戏玩家和内容创作者设计。采用 React + Python + Material UI 构建，支持快速分享游戏录屏、短视频等内容。提供美观仪表板、灵活视频管理、公开/私密分享、文件夹分类、LDAP 认证等功能，完全支持移动设备。支持 Open Graph，分享到社交媒体时自动显示视频信息。Docker 一键部署，轻松搭建个人视频库。"
canonical: https://www.passnat.com/posts/self-hosted-video-sharing-fireshare-deployment-guide-nas-docker-one-click-start-share-game-recordings
---

## 项目介绍

Fireshare 是一款自托管的视频分享平台，专为游戏玩家和内容创作者设计。它采用 React + Python + Material UI 技术栈构建，提供了一个开箱即用的解决方案，让你能够轻松管理和分享视频内容。

相比于上传到 YouTube 等平台需要等待处理和审核的繁琐流程，Fireshare 允许你直接在自己的服务器上部署，将游戏录屏、短视频等内容通过独特链接快速分享给朋友。项目提供了美观的仪表板、灵活的视频管理、公开/私密分享、文件夹分类、用户上传、LDAP 认证等丰富功能，并完全支持移动设备访问。

最棒的是，Fireshare 支持 Open Graph，当你分享链接到社交媒体或聊天应用时，会自动显示视频标题、描述和缩略图，让分享体验更加专业。

项目地址：[点我跳转](https://github.com/ShaneIsrael/fireshare)

## 部署安装

若想在外网也能访问你的 Fireshare 实例，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即使在家外也能随时访问你的视频库。具体配置步骤可参考 [帕斯内网穿透平台上手指南](https://www.passnat.com/posts/p)，全平台支持，新手也能快速上手。

### Docker 一键启动

打开你的 NAS 设备或任何支持 Docker 的服务器，执行以下命令即可快速启动 Fireshare：

```bash
docker run --name fireshare \
  -v $(pwd)/fireshare:/data:rw \
  -v $(pwd)/fireshare_processed:/processed:rw \
  -v /path/to/my_videos:/videos:rw \
  -p 8080:80 \
  -e ADMIN_PASSWORD=your-admin-password \
  -d shaneisrael/fireshare:latest
```

需要修改的地方：

/path/to/my\_videos 替换为你存放视频的实际路径

your-admin-password 替换为你的管理员密码

如果 8080 端口被占用，可改为其他端口（如 8081），格式为 8081:80

启动完成后，在浏览器中访问 http://你的服务器IP:8080 即可进入 Fireshare。

### Docker Compose 部署（可选）

如果你更喜欢使用 Docker Compose，可以复制以下配置文件：

```yaml
version: '3'
services:
  fireshare:
    container_name: fireshare
    image: shaneisrael/fireshare:latest
    ports:
      - '8080:80'
    volumes:
      - ./fireshare_data:/data
      - ./fireshare_processed:/processed
      - ./fireshare_videos:/videos
    environment:
      - ADMIN_USERNAME=admin
      - ADMIN_PASSWORD=admin
      - SECRET_KEY=replace_this_with_some_random_string
      - MINUTES_BETWEEN_VIDEO_SCANS=5
      - THUMBNAIL_VIDEO_LOCATION=0
      - DOMAIN=
      - PUID=1000
      - PGID=1000
```

将上述内容保存为 docker-compose.yml 文件，然后在该文件所在目录执行：

```
docker-compose up -d
```

**重要配置说明：**

**必须修改的环境变量：**

ADMIN\_PASSWORD：管理员登录密码，请设置为强密码

SECRET\_KEY：应用密钥，请替换为随机字符串，用于加密会话数据

**可选配置：**

ADMIN\_USERNAME：管理员用户名，默认为 admin

MINUTES\_BETWEEN\_VIDEO\_SCANS：视频扫描间隔时间（分钟），默认 5 分钟

THUMBNAIL\_VIDEO\_LOCATION：视频缩略图截取位置，0-100 的数值，0 为首帧，50 为中间帧

DOMAIN：你的域名（不含 http/https），用于 Open Graph 功能正常工作，如 v.fireshare.net

PUID 和 PGID：容器运行的用户和组 ID，默认为 1000

**存储卷说明：**

/data：存放 Fireshare 数据库和配置文件，需要持久化

/processed：存放视频元数据和缩略图，需要持久化

/videos：指向你的视频文件夹，Fireshare 会自动扫描此目录

如果端口 8080 与其他服务冲突，可修改配置中的 ports 字段，但容器内部端口 80 保持不变。

## 功能亮点

✨ 美观的管理界面 - 卡片视图和列表视图双重选择

📁 智能文件夹分类 - 自动按目录组织视频内容

🎬 灵活的视频管理 - 编辑标题、描述，控制公开/隐私

📤 社区上传功能 - 支持开放上传或仅限管理员

🔗 一键分享 - 生成独特链接，支持 Open Graph 预览

📱 移动适配 - 完美支持手机和平板设备

🔐 LDAP 认证 - 支持企业级用户目录集成

## 快速开始

部署完成后，使用默认账号 admin 和你设置的密码登录，即可开始上传和管理你的视频。将视频放入映射的 /videos 目录，Fireshare 会自动扫描并生成缩略图。

本篇教程到此结束，祝你使用愉快！🎉

---

原文：<https://www.passnat.com/posts/self-hosted-video-sharing-fireshare-deployment-guide-nas-docker-one-click-start-share-game-recordings>
