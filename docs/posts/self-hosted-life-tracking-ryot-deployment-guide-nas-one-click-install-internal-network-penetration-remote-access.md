---
title: "自托管生活追踪平台Ryot完整部署指南，NAS一键安装，内网穿透远程访问"
date: 2025-10-16
description: "Ryot是功能强大的自托管生活追踪平台，用Rust编写后端性能出色。支持追踪电视剧、书籍、游戏、健身数据，可从Goodreads、Trakt等平台导入，与Jellyfin、Plex等媒体服务器集成。提供直观界面、数据可视化、GraphQL API、OpenID认证和通知推送功能。"
canonical: https://www.passnat.com/posts/self-hosted-life-tracking-ryot-deployment-guide-nas-one-click-install-internal-network-penetration-remote-access
---

## 项目介绍

Ryot（Roll Your Own Tracker，发音为'riot'）是一个功能强大的自托管生活追踪平台。它致力于成为你唯一需要的自托管追踪工具！无论是记录你看过的电视剧、阅读过的书籍、玩过的游戏，还是记录你的健身运动数据，Ryot 都能帮你一站式管理。

这个项目采用 Rust 编写后端，性能极其出色，提供了直观易用的用户界面和美观的数据可视化图表。支持从 Goodreads、Trakt、Strong App 等多个平台导入数据，还能与 Jellyfin、Kodi、Plex、Emby 等媒体服务器集成。同时支持 OpenID Connect 认证、Discord 和 Ntfy 通知推送，以及完整的 GraphQL API 接口。

项目地址： [点我跳转](https://github.com/IgnisDa/ryot)

## 部署安装

如果你想在家庭网络外也能访问这个项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，具体操作指南可以 [点我查看](https://www.passnat.com/posts/p)，帮助你快速上手内网穿透的各种使用方法。

### 使用 Docker 快速部署

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**。

在镜像地址栏填入：`ghcr.io/ignisda/ryot:latest`

等待镜像下载完成后，点击启动按钮启动容器。

### 容器配置

**开机自动启动**：勾选此选项，确保 NAS 重启后容器自动运行。

**端口映射设置**：

容器内部端口：`8000`

主机端口：建议设置为 `8000`（如果该端口已被占用，可修改为其他端口如 `8001`、`8002` 等，但容器内端口保持 `8000` 不变）

**存储位置**：

建议在 NAS 上创建一个专用文件夹用于存储 Ryot 的数据（如 `/mnt/data/ryot`），将其映射到容器内的 `/data` 目录，确保数据持久化保存。

**环境变量设置**：

必须设置：

`DATABASE\_URL`：数据库连接字符串（通常为 `sqlite:///data/ryot.db`）

可选设置（通常无需修改）：

`SERVER\_PORT`：服务器运行端口（默认值：`8000`）

`LOG\_LEVEL`：日志级别（默认值：`info`）

其他环境变量保持默认即可，无需特殊配置。

点击下一步，启动容器。

### 访问应用

容器启动成功后，在浏览器中访问：`http://你的NAS地址:8000`

即可看到 Ryot 的登录界面。首次使用时需要创建账户，之后就可以开始记录和追踪你的媒体消费和健身数据了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/self-hosted-life-tracking-ryot-deployment-guide-nas-one-click-install-internal-network-penetration-remote-access>


## 相关文章

- [Jellyseerr开源影视请求管理系统，NAS一键部署Jellyfin媒体库，内网穿透远程访问审批影视资源](jellyseerr-nas-jellyfin-media-request-approval-system.md)
- [自托管Kyoo影视库服务器，智能识别无需配置，内网穿透远程访问，一次部署永久使用](self-hosted-kyoo-streaming-server-intelligent-recognition-setup-free-internal-network-traversal-remote-access-one-time-deployment-permanent-use.md)
- [Directus 开源数据管理平台 Docker 快速部署，NAS 自建内容管理系统无公网穿透远程访问！](directus-docker-nas-cms-remote-access.md)
- [私有部署开源聊天应用VoceChat，NAS轻松搭建安全私密聊天系统，内网穿透远程访问](vocechat-private-deployment-nas-internal-network-penetration.md)
- [自托管提醒应用MIND，Docker极简部署NAS，80+平台消息推送，内网穿透远程访问](self-hosted-reminder-app-mind-docker-nas-deployment-80-plus-messaging-platforms-internal-penetration-remote-access.md)
