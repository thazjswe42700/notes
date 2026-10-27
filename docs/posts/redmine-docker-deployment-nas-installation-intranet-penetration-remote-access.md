---
title: "Redmine开源项目管理系统Docker部署指南，NAS快速安装配置，内网穿透远程访问完整教程"
date: 2025-10-16
description: "Redmine开源项目管理系统，基于Ruby on Rails框架，提供灵活权限控制、高效问题追踪、完善团队协作功能。支持多项目管理、甘特图表、Wiki、文件管理等，可快速通过Docker部署在NAS设备上运行，适合小型团队和企业级应用的项目管理需求。"
canonical: https://www.passnat.com/posts/redmine-docker-deployment-nas-installation-intranet-penetration-remote-access
---

## 项目介绍

Redmine 是一款功能强大的开源项目管理系统，采用 Ruby on Rails 框架开发，具有跨平台、跨数据库的特性。它以灵活的权限控制、高效的问题追踪、完善的团队协作功能而著称，是众多企业和开源项目的首选管理工具。

无论你是管理小型团队项目，还是运营大规模企业级应用，Redmine 都能提供从需求规划、任务分配、进度追踪到时间统计的全方位解决方案。支持多项目管理、甘特图表、日历视图、Wiki、论坛、文件管理等丰富功能，让团队协作变得更加高效有序。

项目地址：[点我跳转](https://www.redmine.org/)

## 部署安装

如果你需要在外网也能访问 Redmine，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它提供了便捷的内网穿透解决方案。具体的操作步骤可以参考 [这份详细攻略](https://www.passnat.com/posts/p)，支持全平台快速上手。

### 使用 Docker 快速部署

打开你的 NAS 设备（如飞牛 NAS、群晖等）或任何支持 Docker 的系统。

进入本地镜像管理页面，选择「添加镜像」，然后选择「从 URL 添加」的方式。

在镜像地址栏填入：`redmine:latest`

等待镜像下载完成后，点击「启动」按钮创建容器。

**端口配置**

容器启动时需要配置端口映射。Redmine 默认运行在容器内的 **3000 端口**。你可以将其映射到主机的任意端口（如 8080、3000 等）。如果你的主机上 3000 端口已被占用，只需修改映射的主机端口即可，容器内部端口保持 3000 不变。

例如：`主机端口:3000` → `8080:3000`（这样就能通过 `http://ip:8080` 访问）

**存储配置**

Redmine 需要持久化存储来保存数据库和上传的文件。建议在 NAS 上创建一个专用文件夹（如 `/redmine\_data`），然后将其挂载到容器内的 `/usr/src/redmine/files` 目录，确保数据不会因容器重启而丢失。

**环境变量设置**

Redmine 的环境变量配置相对简单，以下是可选的常用设置：

环境变量 | 说明 | 默认值
RAILS\_ENV | 运行环境 | production
REDMINE\_DB\_MYSQL | MySQL 数据库地址 | 无
REDMINE\_DB\_PASSWORD | 数据库密码 | 无

如果你使用 Redmine 自带的 SQLite 数据库，这些变量可以不设置。如果需要连接外部 MySQL 数据库，才需要配置相关的数据库环境变量。

其他参数保持默认即可，直接点击「启动」完成部署。

**访问应用**

部署完成后，在浏览器中访问 `http://你的IP:3000`（如果修改了端口映射，请使用对应的端口号），就能看到 Redmine 的登录界面。

默认管理员账号为 `admin`，密码为 `admin`，首次登录后建议立即修改密码。

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/redmine-docker-deployment-nas-installation-intranet-penetration-remote-access>


## 相关文章

- [无数据库Wiki知识库系统，NAS极简部署DokuWiki，内网穿透远程访问个人文档平台](dokuwiki-nas-intranet-penetration-remote-access.md)
- [Wiki.js 知识库：NAS Docker 部署 + 内网穿透](wikijs-nas-docker-team-knowledge-base-intranet-penetration.md)
- [个人知识库TiddlyWiki5 Docker极简部署NAS无公网内网穿透远程访问](personal-knowledge-base-tiddlywiki5-docker-deployment-nas-intranet-tunneling-remote-access.md)
- [开源自托管项目管理工具Focalboard部署指南，NAS内网穿透远程访问完整教程](focalboard-open-source-self-hosted-project-management-deployment-guide-nas-internal-penetration-remote-access-tutorial.md)
- [MinDoc轻量级文档管理系统Docker极简部署，Golang高性能跨平台支持，内网穿透远程安全访问](mindoc-docker-deployment-golang-intranet-penetration.md)
