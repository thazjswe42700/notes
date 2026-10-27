---
title: "自建联邦式博客引擎Plume NAS部署指南 内网穿透外网访问 Rust开源"
date: 2025-10-16
description: "Plume是基于ActivityPub协议的联邦式博客引擎，采用Rust开发。支持多博客管理、媒体管理、协作写作和跨实例联邦等功能。本文提供Docker快速部署指南，包含镜像添加、容器配置、端口映射、存储设置和环境变量配置等详细步骤，助你轻松在NAS上部署Plume实例，在去中心化网络中自由创作和发布文章。"
canonical: https://www.passnat.com/posts/plume-federated-blog-engine-nas-deployment-guide-intranet-penetration-external-access-rust-opensource
---

## 项目介绍

Plume 是一个基于 ActivityPub 协议的联邦式博客引擎，采用 Rust 语言和 Rocket 框架开发。它不仅仅是一个单独的网站，而是一个相互连接、独立运营的实例网络生态。你可以在自己的实例上创作内容，而整个 Fediverse（联邦宇宙）中的其他实例用户都能够欣赏你的文章。

Plume 的核心特色在于其博客中心化的设计理念——你可以在同一账户下创建多个博客来分类管理不同的发布内容。同时支持媒体管理、协作写作、跨实例联邦等功能，让你的创意在去中心化网络中自由流动。

[点我跳转](https://joinplu.me/)

项目地址：[点我跳转](https://joinplu.me/)

## 部署安装

如果你想在外网也能访问自己部署的 Plume 实例，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的跨平台使用指南可以 [点击这里查看](https://www.passnat.com/posts/p)。

### Docker 快速部署

Plume 提供了 Docker 镜像支持，使得部署变得非常简便。

**第一步：添加 Docker 镜像**

打开你的 NAS 设备（飞牛 NAS 或其他支持 Docker 的设备），进入容器管理界面。

点击'本地镜像' → '添加镜像' → '从 URL 添加'

在镜像地址栏填入：plume:latest

等待镜像下载完成后，点击启动按钮启动容器。

**第二步：容器配置**

启动后会进入容器配置界面，按照以下步骤进行设置：

**开机自启设置**

勾选'开机自动启动'选项，确保 NAS 重启后 Plume 服务能自动恢复。

**端口映射配置**

Plume 默认运行在容器内的 7878 端口。在端口设置中：

容器端口：7878（保持不变）

主机端口：可设置为 7878 或其他未被占用的端口

如果提示端口被占用，你可以修改主机端口号（例如改为 8878），但容器内的端口需要保持为 7878。

**存储配置**

Plume 需要持久化存储数据库和上传的媒体文件。建议在 NAS 上创建一个专用文件夹（如 /plume/data），然后将其映射到容器内的数据目录。

**环境变量设置**

Plume 支持以下环境变量配置：

**必须设置：**

ROCKET\_ADDRESS：服务绑定地址（默认：0.0.0.0）

ROCKET\_PORT：服务端口（默认：7878）

DATABASE\_URL：数据库连接字符串（如使用 PostgreSQL，格式为 postgres://user:password@host/dbname）

**可选设置：**

ROCKET\_ENV：运行环境（development 或 production，默认：production）

ROCKET\_LOG\_LEVEL：日志级别（normal、critical、off）

如果你只是基础使用且不需要复杂配置，可以先不设置环境变量，使用默认配置启动。

**第三步：启动服务**

完成以上配置后，点击'下一步'并确认启动。容器启动成功后，你就可以通过浏览器访问：

```
http://你的NAS_IP:7878
```

（如果修改了主机端口，请使用对应的端口号）

打开后你会看到 Plume 的欢迎界面，可以开始创建账户、创建博客并发布文章了。

本篇教程结束。祝你在 Plume 的去中心化博客世界中尽情创作！

---

原文：<https://www.passnat.com/posts/plume-federated-blog-engine-nas-deployment-guide-intranet-penetration-external-access-rust-opensource>


## 相关文章

- [开源Rust音乐服务器Diosic极简部署，NAS轻量级流媒体解决方案](diosic-rust-music-server-nas-deployment.md)
- [MeiliSearch闪电搜索引擎Docker极速部署，内网穿透远程访问一键搭建](meilisearch-docker-deployment-intranet-penetration-remote-access.md)
- [自建WriteFreely博客平台，NAS极简部署指南，内网穿透远程访问无限制](self-hosted-writefreely-blog-nas-deployment-guide-intranet-penetration-remote-access.md)
- [Oxker Docker容器管理工具TUI界面轻量级终端控制，Rust开发支持实时监控日志查看](oxker-docker-container-management-tui-lightweight-terminal-rust-realtime-monitoring.md)
- [轻量级文件服务器 DUFS 快速部署指南 NAS Docker 一键启用文件共享搜索上传](lightweight-file-server-dufs-quick-deployment-guide-nas-docker-one-click-enable-file-sharing-search-upload.md)
