---
title: "开源仓库管理系统GreaterWMS Docker部署教程 NAS极简搭建完整指南"
date: 2025-10-16
description: "GreaterWMS是开源企业级仓库管理系统，采用Python+Node.js技术栈，集库存、出入库、订单处理于一体。提供Docker快速部署、美观管理界面、跨平台支持，助力中小企业和大型仓储中心实现现代化仓储管理，告别手工管理方式。"
canonical: https://www.passnat.com/posts/open-source-warehouse-management-system-greaterwms-docker-deployment-tutorial-nas-simple-setup-complete-guide
---

## 项目介绍

GreaterWMS 是一款完全开源的仓库管理系统（Warehouse Management System）。这是一个功能完整、生产级别的企业级解决方案，专为现代化仓储管理而设计。项目采用 Python + Node.js 的技术栈，后端基于 Django 框架，前端使用 Quasar 框架构建，支持跨平台部署。

系统集成了完善的库存管理、出入库管理、订单处理等核心功能，拥有美观易用的管理后台界面。无论是中小型企业还是大型仓储中心，都可以通过 GreaterWMS 快速搭建属于自己的仓库管理平台，彻底告别复杂的手工管理方式。

项目地址： [点我跳转](https://github.com/GreaterWMS/GreaterWMS)

## 部署安装

如果你需要在公网上访问这个系统，建议配置内网穿透功能。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能在外网随时随地访问你的仓库管理系统。详细的操作指南可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台上手攻略。

GreaterWMS 提供了开箱即用的 Docker 部署方案，让技术小白也能轻松上手。下面我们使用最简单的 Docker Compose 方式来部署。

### Docker Compose 部署方式

复制下列的配置文件，将其保存为 **docker-compose.yml**：

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:13
    container_name: greaterwms-db
    environment:
      POSTGRES_DB: greaterwms
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: your_secure_password_here
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    restart: unless-stopped

  backend:
    image: greaterwms/backend:latest
    container_name: greaterwms-backend
    depends_on:
      - postgres
    environment:
      DATABASE_URL: postgresql://admin:your_secure_password_here@postgres:5432/greaterwms
      DEBUG: 'False'
      ALLOWED_HOSTS: '*'
    ports:
      - "8000:8000"
    volumes:
      - ./data:/app/data
    restart: unless-stopped

  frontend:
    image: greaterwms/frontend:latest
    container_name: greaterwms-frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
    restart: unless-stopped

volumes:
  postgres_data:
```

打开你的 NAS 设备（如飞牛 NAS、群晖等）或者任何支持 Docker 的系统，将上面的配置文件上传到指定目录。

**重要提示：** 请务必修改 **your\_secure\_password\_here** 为一个强密码，这是数据库的访问密码，关系到你的数据安全。

### 开始部署

在包含 **docker-compose.yml** 文件的目录中，执行以下命令启动所有服务：

```
docker-compose up -d
```

等待容器启动完成，通常需要 1-2 分钟。

### 端口配置说明

系统默认使用以下端口：

前端服务：**3000** 端口（Web 管理界面）

后端 API：**8000** 端口（数据处理）

数据库：**5432** 端口（PostgreSQL 数据库）

如果你的系统中这些端口已被占用，可以修改 **docker-compose.yml** 中的端口映射。例如，如果 3000 端口被占用，可以改为 **"8080:3000"**，这样就能通过 8080 端口访问前端。**注意：冒号前面的数字是你要访问的端口，冒号后面的数字是容器内部的端口，一定不要改动容器内部的端口号。**

### 数据持久化

系统会自动创建 **postgres\_data** 卷来保存数据库数据，以及 **./data** 目录来存储应用数据。这样即使容器重启，你的数据也不会丢失。如果需要自定义存储位置，可以修改配置文件中的 **volumes** 字段。

### 环境变量说明

**必须设置：**

**POSTGRES\_PASSWORD**：数据库密码（强烈建议设置复杂密码）

**DATABASE\_URL**：数据库连接字符串（需要与上面的密码保持一致）

**可选设置：**

**DEBUG**：调试模式（生产环境应设置为 False）

**ALLOWED\_HOSTS**：允许的主机列表（默认为 \* 表示允许所有）

其他配置项保持默认即可，无需额外修改。

### 访问系统

部署完成后，在浏览器中访问：

```
http://你的IP地址:3000
```

例如：**http://192.168.1.100:3000**

你就能看到 GreaterWMS 的登录界面，使用默认账号或你创建的账号即可登录使用。

### 查看日志

如果需要查看系统运行日志，可以执行：

```
docker-compose logs -f
```

按 **Ctrl+C** 可以退出日志查看。

至此，GreaterWMS 仓库管理系统已经成功部署完成！你现在拥有了一个功能完整的企业级仓储管理平台。无论是小型商店还是大型仓储中心，都可以通过这个系统高效地管理你的库存和订单。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/open-source-warehouse-management-system-greaterwms-docker-deployment-tutorial-nas-simple-setup-complete-guide>


## 相关文章

- [Redmine开源项目管理系统Docker部署指南，NAS快速安装配置，内网穿透远程访问完整教程](redmine-docker-deployment-nas-installation-intranet-penetration-remote-access.md)
- [Directus 开源数据管理平台 Docker 快速部署，NAS 自建内容管理系统无公网穿透远程访问！](directus-docker-nas-cms-remote-access.md)
- [Odoo开源ERP系统完整部署指南，企业管理一站式解决方案，内网穿透远程访问](odoo-erp-deployment-guide-enterprise-management-solution.md)
- [开源电子书库管理系统Biblioteca完整部署指南-NAS一键安装Docker Compose配置](open-source-ebook-library-management-system-biblioteca-complete-deployment-guide-nas-one-click-install-docker-compose-configuration.md)
- [Cherry私有视频库管理系统｜Docker快速部署｜NAS内网穿透远程访问影视库](cherry-private-video-library-docker-nas-remote-access.md)
