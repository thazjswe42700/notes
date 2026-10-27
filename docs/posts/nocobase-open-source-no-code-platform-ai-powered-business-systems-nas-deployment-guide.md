---
title: "NocoBase开源无代码平台：AI赋能快速构建业务系统，NAS极简部署完全指南"
date: 2025-10-16
description: "NocoBase是开源无代码平台，融合AI能力，帮助团队快速构建复杂业务系统。采用数据模型驱动设计，提供WYSIWYG体验，支持多数据源和AI员工功能。本文详细介绍NocoBase的功能特性、Docker部署方式、容器配置步骤，包括端口映射、存储配置和环境变量设置，助您快速上手。"
canonical: https://www.passnat.com/posts/nocobase-open-source-no-code-platform-ai-powered-business-systems-nas-deployment-guide
---

## NocoBase：开源无代码平台，赋能团队快速构建业务系统

### 项目介绍

NocoBase 是一个功能强大的开源无代码平台，融合了 AI 能力，致力于帮助团队快速构建复杂的业务系统。它采用数据模型驱动的设计理念，将数据结构与用户界面完全解耦，让你能够创建无限灵活的应用。

相比传统的表单或表格驱动方式，NocoBase 提供了真正的 WYSIWYG（所见即所得）体验。你可以轻松切换使用模式和配置模式，无需编写代码即可创建复杂的业务流程。项目支持主数据库、外部数据库和第三方 API 作为数据源，同时内置了 AI 员工功能，让 AI 能够无缝融入你的业务系统和工作流程中。

NocoBase 采用插件化微内核架构，所有功能都是插件，类似于 WordPress，这意味着你可以根据需要扩展任何功能。无论是页面、数据块、操作、API 还是数据源，都可以通过自定义插件进行扩展。

项目地址：[点我跳转](https://github.com/nocobase/nocobase)

### 部署安装

如果你想在外网也能访问 NocoBase，建议配置内网穿透工具。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，然后参考 [这份攻略](https://www.passnat.com/posts/p) 了解如何在全平台快速上手内网穿透。

#### 使用 Docker 快速部署（推荐）

这是最简单快速的部署方式，只需一条命令即可启动 NocoBase：

```bash
docker run -d \
  --name nocobase \
  -p 8000:8000 \
  -e APP_KEY=your-secret-key \
  -e DB_DIALECT=sqlite \
  nocobase/nocobase:latest
```

参数说明：

-p 8000:8000 - 将容器的 8000 端口映射到主机，如果端口冲突可自行修改前面的端口号

APP\_KEY - 应用密钥，建议设置一个复杂的随机字符串

DB\_DIALECT - 数据库类型，默认使用 SQLite（无需额外配置），也可选择 MySQL、PostgreSQL 等

打开飞牛 NAS 或其他 NAS 设备，进入 Docker 管理界面：

点击 本地镜像 → 添加镜像 → 从 URL 添加

镜像地址填写：nocobase/nocobase:latest

等待镜像下载完成后点击 启动 按钮

容器配置步骤：

勾选 开机自动启动 后进入下一步。

端口映射设置：

在端口配置中，设置容器端口为 8000（这是 NocoBase 应用运行的内部端口），主机端口可根据你的需求设置。如果提示端口被占用，只需修改主机端口号即可，容器内部端口保持为 8000 不变。

存储位置配置：

NocoBase 需要持久化存储应用数据和用户上传的文件。建议创建以下目录映射：

/app/storage - 用于存储应用数据和上传文件

在 NAS 上创建相应文件夹后，将其映射到容器内对应位置。

环境变量设置：

根据你的需求配置以下环境变量：

必须设置：

APP\_KEY - 应用密钥（建议设置复杂的随机字符串，用于加密敏感数据）

可选设置：

DB\_DIALECT - 数据库类型，默认 sqlite，可选 mysql、postgres 等

DB\_HOST - 数据库主机地址（使用 MySQL/PostgreSQL 时需要）

DB\_PORT - 数据库端口（使用 MySQL/PostgreSQL 时需要）

DB\_DATABASE - 数据库名称（使用 MySQL/PostgreSQL 时需要）

DB\_USER - 数据库用户名（使用 MySQL/PostgreSQL 时需要）

DB\_PASSWORD - 数据库密码（使用 MySQL/PostgreSQL 时需要）

APP\_PORT - 应用运行端口，默认 8000

其他环境变量保持默认即可，直接点击 下一步 启动容器。

访问应用：

容器启动成功后，打开浏览器访问 http://你的NAS地址:8000（将 8000 替换为你设置的主机端口），即可看到 NocoBase 的登录界面。首次访问会提示创建管理员账号，按提示完成初始化即可。

到此部署完成！你现在可以开始使用 NocoBase 来构建你的业务应用了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/nocobase-open-source-no-code-platform-ai-powered-business-systems-nas-deployment-guide>


## 相关文章

- [自托管笔记应用Flatnotes极简部署，无数据库Markdown笔记库远程访问方案](flatnotes-self-hosted-markdown-notes-deployment.md)
- [开源低代码平台Budibase快速部署指南，Docker极简搭建应用，内网穿透远程访问](open-source-low-code-platform-budibase-quick-deployment-guide-docker-simple-setup-internal-network-penetration-remote-access.md)
- [开源无代码数据库Baserow自托管部署，Docker一键安装NAS内网穿透远程访问完全指南](open-source-no-code-database-baserow-self-hosted-deployment-docker-one-click-installation-nas-intranet-penetration-remote-access-complete-guide.md)
- [开源无代码平台Joget，NAS极简部署，内网穿透远程访问，快速构建企业应用](open-source-no-code-platform-joget-nas-simple-deployment-intranet-penetration-remote-access-rapid-enterprise-applications.md)
- [开源无代码数据库SeaTable自托管部署指南，NAS极简配置Docker Compose一键启动](seatable-self-hosted-nas-docker-compose-deployment-guide.md)
