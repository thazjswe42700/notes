---
title: "自托管日常任务笔记管理器DailyNotes，Markdown编辑加密存储，Docker一键部署"
date: 2025-10-16
description: "DailyNotes是自托管日常任务与笔记管理器，采用Vue、Flask、TypeScript技术栈构建。支持Markdown编辑、任务清单、搜索功能，数据加密存储。可快速Docker部署到NAS或服务器，提供任务追踪、笔记记录、隐私保护等功能。完美复刻纸质计划本工作流程。"
canonical: https://www.passnat.com/posts/daily-notes-self-hosted-markdown-encrypted-storage-docker
---

## DailyNotes：自托管的日常任务与笔记管理器

#### 项目介绍

DailyNotes 是一款功能强大的自托管日常任务和笔记管理应用，专为那些想要记录每日计划、追踪任务进度的用户而设计。它采用 Vue 前端框架、Flask 后端以及 TypeScript 技术栈构建，支持 Markdown 格式编辑、任务清单管理、搜索功能等丰富特性。

这个项目的灵感来自于传统的纸质计划本使用习惯——每天早上记录需要完成的任务，并在全天随时记录零散的想法和笔记。DailyNotes 完美复刻了这一工作流程，让你可以在任何设备上随时随地管理你的日常事务。

应用采用 Github Flavored Markdown（GFM）格式，原生支持任务列表，并集成 CodeMirror 编辑器提供真正的代码编辑体验，包括代码高亮、文本折叠等高级功能。所有数据都经过加密存储，确保你的隐私安全。

项目地址：[点我跳转](https://github.com/m0ngr31/DailyNotes)

#### 部署安装

如果你需要在公网上访问这个应用，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) —— 注册账号后即可快速建立内外网连接通道，让你在任何地方都能访问家中的应用。具体的配置步骤可以参考 [这份详细指南](https://www.passnat.com/posts/p)。

##### 最快速的部署方式

打开你的 NAS 设备或安装了 Docker 的服务器，使用以下一条命令即可快速启动 DailyNotes：

```bash
docker run -p 5000:5000 -v /config_dir:/app/config m0ngr31/dailynotes
```

**命令说明：**

-p 5000:5000 ：将容器内的 5000 端口映射到主机，这是应用的访问端口

-v /config\_dir:/app/config ：创建一个本地存储目录用于保存数据库和配置文件（请将 /config\_dir 替换为你实际的目录路径，如 /home/user/dailynotes\_config）

如果 5000 端口已被占用，你可以修改前面的端口号，例如 -p 8080:5000 表示用 8080 端口访问应用。

##### 环境变量配置

DailyNotes 支持多个环境变量来自定义应用行为，你可以根据需要添加到 docker run 命令中：

**必须设置的环境变量：**

通常情况下无需强制设置，应用会自动生成必要的密钥

**可选设置的环境变量：**

API\_SECRET\_KEY：用于签署 API 令牌（如不设置会自动生成）

DATABASE\_URI：数据库连接字符串（默认使用 SQLite）

DB\_ENCRYPTION\_KEY：数据加密密钥，长度必须是 16 的倍数（如不设置会自动生成，**重要：修改此值后原有数据将无法解密**）

PREVENT\_SIGNUPS：设置任何值可禁用注册功能（默认允许注册）

⚠ 注意这一项的默认值：**不设 PREVENT\_SIGNUPS 就是允许任何人注册**。实例一旦挂到公网，拿到地址的人都能给自己开个账号。建议的顺序是：先在内网把自己的账号注册好 → 再给 PREVENT\_SIGNUPS 随便设一个值把注册关掉 → 最后才建隧道挂公网。

BASE\_URL：当使用反向代理的子路径时需要设置

PUID：容器运行的用户 ID（用于文件权限管理）

PGID：容器运行的用户组 ID（用于文件权限管理）

对于大多数用户，直接使用基础命令即可，无需额外配置环境变量。

##### 访问应用

部署完成后，在浏览器中访问：

```
http://你的设备IP:5000
```

首次访问时需要创建账户，然后就可以开始记录你的日常任务和笔记了。

#### 项目特色

Markdown 编辑：支持 Github Flavored Markdown 格式，让你用熟悉的方式记录笔记

任务管理：内置任务列表功能，可随时切换任务完成状态

搜索功能：快速查找历史笔记和任务

数据加密：所有数据都经过加密存储，保护你的隐私

自托管：完全控制你的数据，无需依赖第三方服务

跨平台：支持 Docker 部署，兼容各种 NAS 设备和服务器

---

原文：<https://www.passnat.com/posts/daily-notes-self-hosted-markdown-encrypted-storage-docker>


## 相关文章

- [轻量级Markdown笔记应用NoteFlow，本地部署无需数据库，支持内网穿透远程访问笔记管理系统](noteflow-lightweight-markdown-note-app-local-deployment-no-database-intranet-penetration-remote-access.md)
- [PDF文件扫描效果转换器，本地浏览器处理无隐私泄露，支持离线PWA应用，NAS Docker快速部署。](pdf-scan-converter-local-browser-offline-pwa-nas-docker.md)
- [Note Mark 笔记：NAS Docker 部署 + 内网穿透](note-mark-nas-docker-markdown-notes-search-intranet-penetration.md)
- [自托管笔记书签管理Benotes Docker部署指南，NAS轻松搭建私有知识库](self-hosted-notes-bookmarks-benotes-docker-nas-private-knowledge-base.md)
- [自托管笔记应用Flatnotes极简部署，无数据库Markdown笔记库远程访问方案](flatnotes-self-hosted-markdown-notes-deployment.md)
