---
title: "自托管任务管理系统tududi Docker极简部署 本地数据完全私密可控"
date: 2025-10-16
description: "tududi是功能强大的自托管任务管理工具，采用Node.js+React+SQLite现代技术栈。支持任务、项目、笔记、标签多维度管理，内置Telegram集成，支持24种语言和响应式设计。所有数据私密可控，Docker一键快速部署，是完全掌控个人生产力的理想选择。"
canonical: https://www.passnat.com/posts/self-hosted-task-management-tududi-docker-deployment
---

## 项目介绍

tududi 是一款功能强大的自托管任务管理工具，让你完全掌控自己的生产力。这是一个开箱即用、跨平台的生活管理系统，采用现代化的技术栈构建（Node.js + React + SQLite），支持任务、项目、笔记、标签等多维度的组织管理方式。

项目拥有精心设计的层级结构，包括区域（Area）→ 项目（Project）→ 任务（Task）的完整体系。你可以将生活和工作分门别类地组织起来，支持子任务拆解、周期性任务、标签分类等高级功能。特别值得一提的是，tududi 内置了 Telegram 集成，让你可以随时随地通过 Telegram 机器人快速捕捉灵感和待办事项，所有数据都安全地存储在本地，完全私密可控。

项目支持 24 种语言的本地化，拥有响应式设计，无论是桌面、平板还是手机都能完美适配。界面简洁美观，深色和浅色主题任选，让任务管理变得优雅而高效。

项目地址：[点我跳转](https://github.com/chrisvel/tududi)

## 部署安装

如果你想在外网也能访问这个项目，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的使用方法可以参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，轻松实现远程访问。

### Docker 单命令快速启动

tududi 提供了最简便的 Docker 部署方式，只需一条命令即可快速运行。

首先拉取最新镜像：

```bash
docker pull chrisvel/tududi:latest
```

然后执行下面的命令启动容器：

```bash
docker run \
  -e TUDUDI_USER_EMAIL=admin@example.com \
  -e TUDUDI_USER_PASSWORD=mysecurepassword \
  -e TUDUDI_SESSION_SECRET=$(openssl rand -hex 64) \
  -e TUDUDI_ALLOWED_ORIGINS=http://localhost:3002 \
  -e PUID=1001 \
  -e GUID=1001 \
  -v ~/tududi_db:/app/backend/db \
  -v ~/tududi_uploads:/app/backend/uploads \
  -p 3002:3002 \
  -d chrisvel/tududi:latest
```

**参数说明：**

TUDUDI\_USER\_EMAIL：设置管理员登录邮箱（必须设置，比如 admin@example.com）

TUDUDI\_USER\_PASSWORD：设置管理员登录密码（必须设置，请使用强密码）

TUDUDI\_SESSION\_SECRET：会话加密密钥（必须设置，使用 openssl rand -hex 64 生成）

TUDUDI\_ALLOWED\_ORIGINS：允许访问的源地址，本地部署设置为 http://localhost:3002 即可

PUID/GUID：容器运行的用户 ID 和组 ID，默认为 1001，通常无需修改

-v ~/tududi\_db:/app/backend/db：将数据库文件持久化到本地 ~/tududi\_db 目录

-v ~/tududi\_uploads:/app/backend/uploads：将上传文件持久化到本地 ~/tududi\_uploads 目录

-p 3002:3002：映射容器内的 3002 端口到主机的 3002 端口（如果端口冲突可改为其他端口，比如 -p 8888:3002）

**端口说明：**

容器内部应用运行在 3002 端口。如果你的主机上已经有应用占用了 3002 端口，可以修改前面的端口号。例如改为 -p 8888:3002 表示通过主机的 8888 端口访问应用。

**存储说明：**

tududi 需要持久化两个目录：

/app/backend/db：数据库存储目录，包含所有任务、项目、笔记等数据

/app/backend/uploads：文件上传目录，用于存储用户上传的附件

建议在主机上提前创建这两个目录：

```
mkdir -p ~/tududi_db ~/tududi_uploads
```

**环境变量详解：**

**必须设置：**

TUDUDI\_USER\_EMAIL：初始管理员邮箱地址

TUDUDI\_USER\_PASSWORD：初始管理员密码

TUDUDI\_SESSION\_SECRET：会话加密密钥（用 openssl 生成）

**可选设置：**

TUDUDI\_ALLOWED\_ORIGINS：CORS 跨域配置，不设置时仅允许 localhost 访问；设置为特定域名如 https://tududi.yourdomain.com 可用于反向代理场景

PUID/GUID：容器运行用户，默认 1001

DB\_FILE：数据库文件路径，默认为 db/production.sqlite3

TUDUDI\_UPLOAD\_PATH：上传文件存储路径，默认为 /app/backend/uploads

其他变量保持默认即可，无需修改。

### 启动后访问

执行完上述命令后，稍等片刻让容器完全启动。然后在浏览器中访问：

```
http://localhost:3002
```

使用你设置的邮箱和密码登录，就可以开始使用 tududi 了。如果你修改了端口映射，比如改为 8888，则访问 http://localhost:8888。

### Telegram 集成（可选）

tududi 支持 Telegram 集成，可以通过 Telegram 机器人快速创建任务。这是完全可选的功能，部署后可在设置中配置。

如果你想启用 Telegram 功能：

在 Telegram 中与 @BotFather 聊天，使用 /newbot 命令创建一个新机器人

获得机器人 Token 后，登录 tududi 进入设置 → Telegram 标签页

粘贴你的 Bot Token，点击'Setup Telegram'按钮完成配置

之后就可以通过 Telegram 向机器人发送消息，自动创建任务了

## 部署完成

至此，tududi 任务管理系统已经成功部署完毕。你现在拥有了一个功能完整的自托管生产力工具，所有数据都掌握在自己手中。无论是个人任务管理、项目追踪，还是团队协作，tududi 都能满足你的需求。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/self-hosted-task-management-tududi-docker-deployment>


## 相关文章

- [自动签到脚本DailyCheckIn Docker部署指南，支持NAS群晖青龙面板内网穿透远程管理](auto-checkin-script-dailycheckin-docker-deployment-nas-synology-qinglong-panel-intranet-penetration-remote-management.md)
- [NAS搭建PanSou网盘资源搜索引擎完整教程：TG频道54插件并发搜索Docker快速部署内网穿透远程访问](pansoutg54nasdocker.md)
- [私密日记极简部署，纯文本记录无数据库，Hibiscus.txt Docker内网穿透随处访问](hibiscus-txt-private-diary-docker-deployment.md)
- [SideQuests 任务管理系统 Docker 一键部署，NAS 极简配置内网穿透远程访问任务](sidequests-docker-nas-deployment-intranet-penetration.md)
- [个人财务管理系统BudgetBee，Docker一键部署，NAS极简配置，多账户记账工具](budget-bee-docker-deployment-nas-multi-account-accounting.md)
