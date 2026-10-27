---
title: "轻量级Markdown笔记应用NoteFlow，本地部署无需数据库，支持内网穿透远程访问笔记管理系统"
date: 2025-10-16
description: "NoteFlow是轻量级Markdown笔记应用，集笔记管理与任务追踪于一身。基于FastAPI构建，无需数据库，所有数据存储在本地。支持丰富Markdown语法、任务清单、图片附件、链接存档、数学公式等功能。提供多种主题选择，简洁优雅的界面让你轻松创建和管理笔记。隐私优先，完全本地运行，你的笔记永远不会离开你的机器。"
canonical: https://www.passnat.com/posts/noteflow-lightweight-markdown-note-app-local-deployment-no-database-intranet-penetration-remote-access
---

## 项目介绍

NoteFlow 是一款轻量级、基于 Markdown 的笔记应用，集笔记管理与任务追踪于一身。它采用 FastAPI 后端构建，提供简洁优雅的界面，让你可以轻松创建、查看和管理笔记。所有笔记数据存储在一个单一的 Markdown 文件中，形成自然的时间线流。

项目完全基于本地运行，无需数据库，你的笔记永远不会离开你的机器。支持丰富的 Markdown 语法、任务清单、图片和文件附件、网站链接存档、数学公式渲染等功能。界面提供多种主题选择，既有现代风格也有复古设计。

项目地址： [点我跳转](https://github.com/Xafloc/NoteFlow)

## 部署安装

若要在外网访问你的 NoteFlow 笔记应用，建议配置内网穿透服务以实现安全远程访问。推荐使用 [帕斯内网穿透](https://passnat.com)，先完成账号注册备用。具体的配置步骤可以 [参考这篇教程](https://www.passnat.com/posts/p)，详细讲解了内网穿透在各平台上的快速上手方法。

### 最快速部署方案

NoteFlow 支持多种安装方式，最简单的是使用 Docker 容器化部署。

**使用 Docker 单命令启动：**

打开你的 NAS 设备或任何支持 Docker 的系统，进入本地镜像管理界面，选择从 URL 添加镜像。

在镜像地址栏填入：`xafloc/noteflow:latest`

等待镜像下载完成后，点击启动按钮创建容器。

**容器配置说明：**

勾选开机自动启动选项以确保服务持久运行。

**端口映射设置：**

NoteFlow 的应用运行在容器内的 8000 端口。在端口映射配置中，将容器的 8000 端口映射到宿主机的 8000 端口（或其他未被占用的端口）。如果 8000 端口已被占用，你可以将宿主机端口改为其他值，例如 8001、8002 等，但容器内部端口需保持为 8000。

**存储卷挂载：**

NoteFlow 会在指定目录下生成 `notes.md` 笔记文件和 `assets` 资源文件夹（包含图片、文件和网站存档）。建议在 NAS 上创建一个专用文件夹（例如 `/mnt/noteflow`），将其映射为容器内的 `/app/data` 目录，以便持久化保存你的所有笔记和附件。

**环境变量设置：**

NoteFlow 无需强制配置环境变量，所有功能开箱即用。如无特殊需求，可直接跳过此步骤。

**启动完成：**

容器启动成功后，在浏览器中访问 `http://你的设备IP:8000`（如果修改了宿主机端口，请使用对应的端口号），即可看到 NoteFlow 的主界面，开始记录你的笔记。

通过上述步骤，你已经成功部署了 NoteFlow。现在可以开始享受这款零配置、隐私优先的笔记应用了。所有内容都保存在本地，完全由你掌控。

本篇教程结束

---

原文：<https://www.passnat.com/posts/noteflow-lightweight-markdown-note-app-local-deployment-no-database-intranet-penetration-remote-access>
