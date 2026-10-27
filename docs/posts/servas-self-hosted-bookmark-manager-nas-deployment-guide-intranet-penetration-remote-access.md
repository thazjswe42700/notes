---
title: "自托管书签管理工具Servas无公网部署指南 NAS极简配置内网穿透远程访问"
date: 2025-10-16
description: "自托管书签管理工具Servas，采用Laravel和Inertia.js构建，支持标签分类、嵌套分组、浏览器扩展、多用户账户、双因素认证等功能。提供Docker快速部署，配置简单，数据安全本地存储，是整理和管理个人书签的最佳解决方案。"
canonical: https://www.passnat.com/posts/servas-self-hosted-bookmark-manager-nas-deployment-guide-intranet-penetration-remote-access
---

## Servas - 自托管书签管理工具

### 项目介绍

Servas 是一款功能强大的自托管书签管理工具，基于 Laravel 和 Inertia.js 构建，采用 Tailwind CSS 和 Svelte 打造现代化前端界面。无论你是想整理浏览器中的数百个书签，还是建立个人知识库，Servas 都能为你提供优雅高效的解决方案。

这个项目支持标签分类、嵌套分组、智能分组、浏览器扩展、多用户账户、响应式设计、导入导出、深色/浅色主题切换以及双因素认证等丰富功能。配合 Firefox 和 Chrome 浏览器扩展，你可以随时随地快速保存网页到 Servas，让书签管理变得轻而易举。

项目地址：[点我跳转](https://github.com/beromir/Servas)

### 部署安装

如果你需要在外网也能访问到 Servas，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备好。具体操作步骤可以参考 [这份详细攻略](https://www.passnat.com/posts/p)，帮助你快速上手全平台内网穿透方案。

#### Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板，按照以下步骤操作：

**第一步：准备配置文件**

在你选定的目录中创建 *.env* 文件，复制以下内容：

```
APP_NAME=Servas
APP_ENV=production
APP_KEY=
APP_DEBUG=false
APP_URL=https://your-servas-instance

SERVAS_ENABLE_REGISTRATION=true
SERVAS_SHOW_APP_VERSION=true

# SQLite
DB_CONNECTION=sqlite
DB_DATABASE=/app/database/database.sqlite
DB_FOREIGN_KEYS=true
```

同时在同一目录创建 *docker-compose.yml* 文件，复制以下配置：

```yaml
version: '3'

services:
  servas:
    image: beromir/servas
    container_name: servas
    restart: unless-stopped
    ports:
      - '8080:80'
    volumes:
      - ./.env:/app/.env
      - servas-db-sqlite:/app/database/database.sqlite

volumes:
  servas-db-sqlite:
```

**第二步：启动容器**

在配置文件所在目录打开终端，执行以下命令启动 Servas：

```bash
docker compose up -d
```

**第三步：生成应用密钥**

容器启动后，需要生成应用密钥来保护你的数据：

```bash
docker exec -it servas php artisan key:generate --force
```

**第四步：重启容器**

```bash
docker compose restart
```

**第五步：创建用户账户**

打开浏览器，访问 *http://你的IP:8080/register*，创建你的第一个用户账户。

#### 端口配置说明

默认配置中，Servas 运行在容器的 80 端口，映射到主机的 8080 端口。如果 8080 端口已被占用，你可以修改 *docker-compose.yml* 中的这一行：

```
ports:
  - '8080:80'  # 修改前面的数字即可，比如改成 '3000:80'
```

修改后重新运行 *docker compose up -d* 即可生效。

#### 数据持久化

项目使用 SQLite 数据库存储所有书签数据。Docker 已经配置了数据卷 *servas-db-sqlite* 来持久化保存数据库文件，确保容器重启或更新后数据不会丢失。

#### 环境变量说明

**必须配置：**

APP\_URL：修改为你的实际访问地址（如 *https://servas.example.com*），确保应用正常工作

**可选配置：**

SERVAS\_ENABLE\_REGISTRATION：是否允许新用户注册（true/false，默认 true）

SERVAS\_SHOW\_APP\_VERSION：是否在用户菜单中显示版本号（true/false，默认 true）

其他变量保持默认即可，无需修改。

#### 访问应用

部署完成后，在浏览器中访问 *http://你的服务器IP:8080* 即可打开 Servas 界面。如果是外网访问，使用配置好的内网穿透地址即可。

本篇教程结束

---

原文：<https://www.passnat.com/posts/servas-self-hosted-bookmark-manager-nas-deployment-guide-intranet-penetration-remote-access>
