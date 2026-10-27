---
title: "开源评论引擎Comentario一键部署指南，NAS轻松搭建私人讨论系统，Docker快速启动"
date: 2025-10-16
description: "开源评论引擎Comentario完整部署指南。Comentario是隐私优先的网页评论系统，支持多语言、社交登录、分层评论、Markdown格式、邮件通知和评论投票。本文详细介绍Docker Compose快速部署方法，包括PostgreSQL数据库配置、端口设置、环保存储持久化和初始化步骤，帮助博客作者和网站管理员轻松搭建安全的评论平台。"
canonical: https://www.passnat.com/posts/open-source-comment-engine-comentario-deployment-guide-nas-private-discussion-system-docker-quick-start
---

## 项目介绍

Comentario 是一款开源的网页评论引擎，专为静态网站和内容平台设计。它能够为任何普通的网页添加强大的讨论功能，让访客可以在网站上进行互动和评论。

Comentario 具有隐私优先的设计理念，不包含任何追踪脚本或广告像素，同时支持多语言界面、灵活的权限管理、多种登录方式（包括 Google、GitHub、GitLab、Facebook、Twitter/X 等社交账号登录）、分层评论、Markdown 格式支持、邮件通知、实时更新、评论投票、主持人审核等丰富功能。

无论你是博客作者、内容创作者还是网站管理员，Comentario 都能为你的平台提供一个安全、隐私友好且功能完整的评论系统。

项目地址：[点我跳转](https://gitlab.com/comentario/comentario)

## 部署安装

如果你想在外网也能访问本地部署的 Comentario，建议配置内网穿透功能。推荐使用[帕斯内网穿透](https://passnat.com)服务，先注册账号备用。具体操作步骤可参考[帕斯内网穿透全平台快速上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

### 使用 Docker Compose 快速部署

这是最简便的部署方式，只需复制下面的配置文件即可。

复制下列的配置文件到你的服务器或 NAS 设备：

```yaml
version: '3.8'

services:
  # PostgreSQL 数据库服务
  db:
    image: postgres:17-alpine
    container_name: comentario-db
    environment:
      POSTGRES_DB: comentario
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - "5432:5432"
    volumes:
      - comentario-db:/var/lib/postgresql/data
    restart: unless-stopped

  # Comentario 主应用
  comentario:
    image: registry.gitlab.com/comentario/comentario:latest
    container_name: comentario-app
    ports:
      - "8080:80"
    environment:
      # 数据库连接配置
      COMENTARIO_DB_HOST: db
      COMENTARIO_DB_PORT: 5432
      COMENTARIO_DB_USER: postgres
      COMENTARIO_DB_PASSWORD: postgres
      COMENTARIO_DB_NAME: comentario
      # 服务器配置
      COMENTARIO_HOST: 0.0.0.0
      COMENTARIO_PORT: 80
    depends_on:
      - db
    restart: unless-stopped

volumes:
  comentario-db:
```

**部署步骤：**

打开你的飞牛 NAS、群晖 NAS 或其他 NAS 设备的管理界面。

进入 Docker 应用管理模块，选择「从 URL 导入」或「从文件导入」的方式，将上述 docker-compose 配置粘贴进去。

系统会自动下载 PostgreSQL 17 和 Comentario 最新镜像，这个过程可能需要几分钟，请耐心等待。

镜像下载完成后，点击启动按钮启动容器组。

勾选「开机自动启动」选项，确保 NAS 重启后服务能自动恢复。

**端口配置说明：**

8080 端口：这是 Comentario 应用的访问端口。如果你的 NAS 上已有其他服务占用了 8080 端口，可以修改配置中的 8080 为其他未使用的端口（如 8081、8082 等），但冒号后的 80 需要保持不变，这是容器内部的端口。

5432 端口：PostgreSQL 数据库端口。如果冲突可改为其他端口如 5433:5432。

**存储持久化配置：**

上述配置中已包含 comentario-db 数据卷，用于持久化保存数据库数据。所有评论、用户信息和配置都会被保存在这个卷中，即使容器重启也不会丢失。

**环境变量设置说明：**

必须设置：

POSTGRES\_PASSWORD：PostgreSQL 数据库密码，请修改为强密码

COMENTARIO\_DB\_PASSWORD：与上面保持一致

可选设置（保持默认即可）：

COMENTARIO\_HOST：绑定的主机地址，默认 0.0.0.0（允许外部访问）

COMENTARIO\_PORT：容器内部端口，默认 80

其他配置项都可以保持默认，直接启动即可。

**访问应用：**

容器启动成功后，打开浏览器访问 http://你的NAS地址:8080，即可看到 Comentario 的管理界面和演示页面。

首次访问时会引导你进行初始化设置，包括创建管理员账户、配置评论域名等。

到这里，Comentario 就已经成功部署完毕了！你现在可以开始配置你的评论系统，为你的网站添加互动评论功能。

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-comment-engine-comentario-deployment-guide-nas-private-discussion-system-docker-quick-start>
