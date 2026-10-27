---
title: "自托管私人图书馆管理系统BookLogr，NAS极简部署，内网穿透随处访问阅读数据"
date: 2025-10-16
description: "探索BookLogr个人图书馆管理系统，自托管网络应用让你完全掌控阅读数据。支持OpenLibrary快速查询书籍、评分记笔记、生成公开主页分享阅读成就。Docker一键部署到NAS，提供安全私密的书籍管理体验，支持多格式导出和Mastodon分享功能。"
canonical: https://www.passnat.com/posts/self-hosted-private-library-management-booklogr-nas-deployment-intranet-access
---

## 项目介绍

BookLogr 是一款功能强大的个人图书馆管理系统，专为书籍爱好者量身定制。这是一个自托管的网络应用，让你能够完全掌控自己的阅读数据，提供安全私密的书籍管理体验。无论是记录已读的书籍、标记想要阅读的清单，还是分享你的阅读成就，BookLogr 都能帮你轻松搞定。

项目支持通过 OpenLibrary 快速查询书籍信息，你可以按标题或 ISBN 号轻松找到想要的书。除此之外，还能为书籍评分、记录阅读进度、撰写读书笔记和摘抄精彩语句。如果你想展示自己的阅读品味，还可以生成公开的个人图书馆主页分享给朋友。项目甚至支持将阅读进度自动分享到 Mastodon，以及导出多种格式的数据（CSV、JSON、HTML）。

[点我跳转](https://github.com/Mozzo1000/booklogr)

项目地址：

## 部署安装

如果你想在外网也能随时访问自己的图书馆，可以使用内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体操作可以参考 [内网穿透全平台快速入门指南](https://www.passnat.com/posts/p)。

### Docker Compose 一键部署

这是最推荐的部署方式，适合大多数用户。

#### 第一步：复制配置文件

将以下配置保存为 `docker-compose.yml` 文件：

```yaml
services:
    booklogr-api:
        container_name: 'booklogr-api'
        image: mozzo/booklogr:v1.6.0
        environment:
            - DATABASE_URL=sqlite:///books.db
            - AUTH_SECRET_KEY=this-really-needs-to-be-changed
        ports:
            - 5000:5000
        volumes:
            - ./data:/app/instance

    booklogr-web:
        container_name: 'booklogr-web'
        image: mozzo/booklogr-web:v1.6.0
        environment:
            - BL_API_ENDPOINT=http://localhost:5000/
            - BL_GOOGLE_ID=
            - BL_DEMO_MODE=false
        ports:
            - 5150:80
```

#### 第二步：在 NAS 中部署

打开你的飞牛 NAS 或其他 NAS 设备，进入 Docker 管理界面。

#### 第三步：配置参数说明

**AUTH\_SECRET\_KEY**：这是 API 的密钥，**必须修改**。建议改成一个复杂的随机字符串，用于保护你的数据安全。

**BL\_API\_ENDPOINT**：Web 前端访问 API 的地址。如果在外网访问，需要改成你的外网 IP 或域名（例如：`http://your-ip:5000/`）。

**BL\_GOOGLE\_ID**：Google 登录的 Client ID，可选。如果不需要 Google 登录功能，留空即可。

**BL\_DEMO\_MODE**：演示模式开关，默认关闭，保持 `false` 即可。

#### 第四步：端口配置

**API 服务端口**：5000（容器内部端口），映射到主机的 5000 端口。如果 5000 端口已被占用，可修改为其他端口（如 5001），格式为 `5001:5000`。

**Web 服务端口**：5150（主机端口），容器内部为 80。如果 5150 被占用，可改为其他端口（如 5151），格式为 `5151:80`。

#### 第五步：数据存储

项目会在 `./data` 目录下存储所有书籍数据和数据库文件。NAS 会自动创建此目录，无需手动操作。

#### 第六步：启动服务

在 NAS 的 Docker 管理界面中，选择启动容器。勾选'开机自动启动'选项，这样 NAS 重启后服务会自动运行。

## 访问应用

部署完成后，打开浏览器访问：

Web 界面：`http://你的NAS-IP:5150`

API 地址：`http://你的NAS-IP:5000`

首次访问时，系统会引导你完成初始设置。之后你就可以开始添加书籍、记录阅读进度、评分和撰写笔记了。

## 总结

BookLogr 让书籍管理变得简单高效。通过 Docker Compose 的一键部署，即使是技术小白也能在几分钟内搭建自己的私密图书馆。配合内网穿透，你可以在任何地方随时访问和管理自己的阅读记录。开始使用 BookLogr，让阅读变得更有趣！

本篇教程结束。

---

原文：<https://www.passnat.com/posts/self-hosted-private-library-management-booklogr-nas-deployment-intranet-access>
