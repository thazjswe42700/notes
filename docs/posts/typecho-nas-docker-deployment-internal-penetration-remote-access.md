---
title: "轻量级PHP博客系统Typecho在NAS上Docker部署，内网穿透远程访问详细教程指南"
date: 2025-10-16
description: "Typecho是轻量级高效的PHP博客系统，支持MySQL、PostgreSQL、SQLite等多种数据库，内置Markdown编辑器、强大插件和主题系统。本教程详细介绍如何在NAS上使用Docker快速部署Typecho，包括镜像添加、容器配置、端口映射、存储设置和环境变量配置等完整步骤，帮助你轻松搭建个性化博客平台。"
canonical: https://www.passnat.com/posts/typecho-nas-docker-deployment-internal-penetration-remote-access
---

## 项目介绍

Typecho 是一款轻量级、高效能的 PHP 博客系统，以其简洁优雅的设计理念和强大的功能特性著称。作为全球最具竞争力的博客引擎之一，Typecho 支持多种数据库后端（包括 MariaDB、MySQL、SQLite 和 PostgreSQL），内置 Markdown 编辑器、强大的插件系统和主题系统，让你可以轻松打造个性化的博客平台。

无论你是资深博主还是初出茅庐的写作爱好者，Typecho 都能为你提供一个稳定、灵活且易于扩展的内容管理平台。

项目地址：[点我跳转](https://github.com/typecho/typecho)

## 部署安装

若需要在外网访问你的博客，建议配置内网穿透服务，推荐使用 [帕斯内网穿透](https://passnat.com)。先注册账号备用，具体操作步骤可参考 [全平台快速上手指南](https://www.passnat.com/posts/p)。

### 使用 Docker 快速部署

打开你的 NAS 设备（如飞牛 NAS 或其他 Docker 支持的 NAS 系统）

点击 **本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：`typecho/typecho:latest`

等待镜像下载完成后，点击 **启动** 按钮启动容器

### 容器配置说明

开机自启设置

勾选 **开机自动启动**，确保 NAS 重启后博客服务自动运行

端口映射配置

容器内部运行在 **80 端口**（HTTP）。在 NAS 中配置端口映射时，你可以：

保持默认映射：`80:80`（直接使用 80 端口访问）

自定义映射：`8080:80`（通过 8080 端口访问，如果 80 端口已被占用）

存储位置配置

建议在 NAS 上创建一个专用文件夹（如 `/data/typecho`），用于存储博客数据和用户上传的文件。将此文件夹映射到容器内的 `/app` 目录，确保数据持久化保存。

环境变量设置

Typecho 的基础配置可通过以下环境变量设置：

必须设置

DB\_TYPE：数据库类型（mysql、pgsql、sqlite 等）

DB\_HOST：数据库服务器地址

DB\_USER：数据库用户名

DB\_PASSWORD：数据库密码

DB\_NAME：数据库名称

可选设置

DB\_PORT：数据库端口（默认根据数据库类型自动设置）

如无特殊需求，可使用内置的 SQLite 数据库，此时无需设置上述变量。

### 访问你的博客

容器启动成功后，在浏览器中访问：`http://你的NAS地址:80`

根据提示完成初始化配置，即可开始使用 Typecho 博客系统。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/typecho-nas-docker-deployment-internal-penetration-remote-access>
