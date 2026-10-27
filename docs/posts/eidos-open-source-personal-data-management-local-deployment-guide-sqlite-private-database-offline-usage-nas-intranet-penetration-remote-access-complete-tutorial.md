---
title: "开源个人数据管理框架Eidos本地部署指南，SQLite私密数据库离线使用，NAS内网穿透远程访问完整教程"
date: 2025-10-16
description: "Eidos是开源个人数据管理框架，基于SQLite构建的离线优先型口袋数据库。集Notion文档、AI智能功能和扩展系统于一身，支持本地存储、完全隐私、数据翻译和智能交互。本文详解Docker快速部署步骤、端口配置方法和数据持久化设置，助力远程访问。"
canonical: https://www.passnat.com/posts/eidos-open-source-personal-data-management-local-deployment-guide-sqlite-private-database-offline-usage-nas-intranet-penetration-remote-access-complete-tutorial
---

## 项目介绍

Eidos 是一个开源的个人数据管理框架，专为终身数据管理而设计。它将 SQLite 转变为一个功能强大的个人口袋数据库，让每个人都能轻松使用。这是一个类似 Notion 的文档和数据库管理系统，集成了现代化的 AI 功能，支持离线工作、数据扩展和开放格式存储。

Eidos 提供了一个全面的个人数据管理框架，你可以在一个地方组织、存储和管理所有个人数据。所有数据都存储在本地，无需互联网连接即可访问，确保了闪电般的性能和完全的隐私保护。同时内置 AI 功能，可以实现数据翻译、总结和智能交互。最棒的是，它拥有强大的扩展系统，你可以通过编写代码或让 AI 生成扩展代码来定制功能。

项目地址：[点我跳转](https://github.com/mayneyao/eidos)

## 部署安装

如果你想在外出时也能访问本地部署的 Eidos，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以参考 [这份详细的上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

### 使用 Docker 快速部署

打开你的 NAS 设备或其他支持 Docker 的系统，进入本地镜像管理界面。

点击 **添加镜像 - 从 URL 添加**，在镜像地址栏填入：

```
mayneyao/eidos:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

**勾选开机自动启动**，这样每次重启系统都会自动启动 Eidos。

### 端口配置

容器内部运行在 **80 端口**。在 NAS 管理界面中，设置端口映射：

容器端口：80

宿主机端口：3000（或你喜欢的其他端口）

如果 3000 端口已被占用，可以修改宿主机端口号，但容器内的 80 端口保持不变。

### 环境变量设置

Eidos 无需特殊的环境变量配置，保持默认设置即可正常运行。

### 存储位置

Eidos 会将所有数据存储在容器内部的 SQLite 数据库中。如果你需要持久化数据，建议创建一个本地文件夹（例如 /data/eidos）并将其映射到容器内的 /app/data 目录，确保容器重启后数据不会丢失。

直接点击启动即可完成部署。

### 访问应用

部署完成后，在浏览器中访问：

```
http://你的NAS_IP:3000
```

即可打开 Eidos 应用，开始管理你的个人数据。

本篇教程结束

---

原文：<https://www.passnat.com/posts/eidos-open-source-personal-data-management-local-deployment-guide-sqlite-private-database-offline-usage-nas-intranet-penetration-remote-access-complete-tutorial>
