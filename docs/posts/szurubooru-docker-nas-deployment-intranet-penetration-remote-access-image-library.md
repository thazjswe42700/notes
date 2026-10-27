---
title: "自建图片社区平台Szurubooru，Docker极简部署NAS，内网穿透远程访问图片库"
date: 2025-10-16
description: "Szurubooru是功能强大的开源图片社区引擎，支持图片、视频、Flash等多种格式。提供JSON REST API、强大搜索、权限管理、标签分类等功能。界面简洁美观，部署快速简单，特别适合搭建个人收藏库或社区内容平台。本教程详细讲解Docker Compose快速部署步骤。"
canonical: https://www.passnat.com/posts/szurubooru-docker-nas-deployment-intranet-penetration-remote-access-image-library
---

## 项目介绍

Szurubooru 是一个功能强大的图片社区引擎，专为中小型社区量身打造。它支持多种内容格式，包括图片（JPG、PNG、GIF、动图）、视频（MP4、WEBM）以及 Flash 动画。项目提供了丰富的 JSON REST API、强大的搜索系统、灵活的权限管理、标签分类、标签建议、标签关联、池管理等高级功能。无论是建立个人收藏库还是社区内容平台，Szurubooru 都能满足你的需求。

界面简洁美观，部署简单快速，特别适合想要搭建自己的图片社区但又不想过于复杂的用户。

项目地址：[点我跳转](https://github.com/rr-/szurubooru)

## 部署安装

如果你想在外网也能访问你的项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地访问你的图片社区了。具体的配置方法可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台上手攻略。

### 快速开始 - 使用 Docker Compose

Szurubooru 推荐使用 Docker Compose 进行部署，这是最便捷的方式。

**第一步：准备配置文件**

复制下列的 Docker Compose 配置文件到你的 NAS 或服务器中：

```yaml
services:
  server:
    image: szurubooru/server:latest
    depends_on:
      - sql
    environment:
      POSTGRES_HOST: sql
      POSTGRES_USER: szurubooru
      POSTGRES_PASSWORD: your_secure_password_here
      THREADS: 4
    volumes:
      - "${MOUNT_DATA}:/data"
      - "./server/config.yaml:/opt/app/config.yaml"

  client:
    image: szurubooru/client:latest
    depends_on:
      - server
    environment:
      BACKEND_HOST: server
      BASE_URL: ""
    volumes:
      - "${MOUNT_DATA}:/data:ro"
    ports:
      - "${PORT}:80"

  sql:
    image: postgres:11-alpine
    restart: unless-stopped
    environment:
      POSTGRES_USER: szurubooru
      POSTGRES_PASSWORD: your_secure_password_here
    volumes:
      - "${MOUNT_SQL}:/var/lib/postgresql/data"
```

**第二步：打开你的 NAS 设备**

打开飞牛 NAS 或其他 NAS 设备的 Docker 管理界面。

**第三步：配置环境变量**

在启动容器前，需要设置以下环境变量。你可以创建一个 `.env` 文件，或在 NAS 的 Docker 管理界面中直接填写：

必须设置：

POSTGRES\_USER: 数据库用户名（建议设置为 `szurubooru`）

POSTGRES\_PASSWORD: 数据库密码（请设置一个强密码）

MOUNT\_DATA: 数据存储路径（例如 `/mnt/data/szurubooru`）

MOUNT\_SQL: 数据库存储路径（例如 `/mnt/data/szurubooru-db`）

PORT: 访问端口（默认 `80`，如果冲突可改为 `8080` 等）

可选设置：

THREADS: 工作线程数（默认自动，可根据服务器性能调整）

BASE\_URL: 如果通过反向代理访问，设置基础 URL（例如 `/szurubooru/`）

**第四步：配置服务器参数**

在启动前，需要准备 `server/config.yaml` 配置文件。复制 `config.yaml.dist` 为 `config.yaml`，并根据需要修改以下重要字段：

secret: 应用密钥（请设置一个随机字符串）

smtp 部分：如需邮件功能，配置 SMTP 服务器信息

**第五步：端口映射**

在 NAS 的 Docker 界面中，设置容器端口映射。将容器内的 **80 端口**（或你在 `PORT` 变量中设置的端口）映射到主机的相应端口。如果提示端口被占用，可以修改主机端口号，但容器内的端口需要保持为 80。

**第六步：存储位置配置**

创建两个文件夹用于数据持久化：

一个用于存储图片和用户数据（映射到 `MOUNT\_DATA`）

一个用于存储数据库文件（映射到 `MOUNT\_SQL`）

在 NAS 中创建这两个目录，然后在 Docker 配置中正确映射。

**第七步：启动应用**

所有配置完成后，点击启动按钮启动容器。建议勾选'开机自动启动'，这样重启后应用会自动运行。

**第八步：访问应用**

创建成功后，打开浏览器访问 `http://你的NAS\_IP:PORT`（其中 PORT 是你设置的端口号，默认为 80）。首次访问时，系统会引导你完成初始化设置，包括创建管理员账户。

到这里就可以正常使用 Szurubooru 图片社区了！

本篇教程结束

---

原文：<https://www.passnat.com/posts/szurubooru-docker-nas-deployment-intranet-penetration-remote-access-image-library>


## 相关文章

- [开源时间追踪Kimai完整部署指南,NAS极简配置轻松打造专业计时系统](kimai-open-source-time-tracking-deployment-guide-nas-simple-configuration.md)
- [ZJSON在线JSON格式化编辑工具，支持转义压缩对比，NAS Docker一键部署，内网穿透远程访问使用](json-formatter-online-tool-escape-compress-compare-nas-docker-deployment-intranet-penetration.md)
- [浏览器WebRTC视频通话应用Call-Me，NAS一键部署，支持屏幕共享与内网穿透远程通话](browser-webrtc-video-call-app-call-me-nas-one-click-deployment-screen-sharing-intranet-penetration-remote-calling.md)
- [私密日记极简部署，纯文本记录无数据库，Hibiscus.txt Docker内网穿透随处访问](hibiscus-txt-private-diary-docker-deployment.md)
- [自托管短链接服务Snapp，私密数据掌控无忧，NAS极简部署零烦恼，内网穿透远程访问](self-hosted-url-shortener-snapp-private-data-nas-deployment-intranet-penetration.md)
