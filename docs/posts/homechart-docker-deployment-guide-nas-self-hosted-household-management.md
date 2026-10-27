---
title: "开源家庭管理平台Homechart Docker部署指南 NAS自托管家务预算日程统一管理"
date: 2025-10-16
description: "Homechart是一个功能全面的开源家庭管理平台，支持日程安排、膳食规划、家务分配和预算管理。支持多语言、无广告、数据加密，适合各类家庭结构。本文详细介绍Homechart的Docker部署方案和Docker Compose完整安装步骤。"
canonical: https://www.passnat.com/posts/homechart-docker-deployment-guide-nas-self-hosted-household-management
---

## 项目介绍

Homechart 是一个强大的家庭管理平台，被誉为'家庭的任务控制中心'。它是一款基于网络的、功能全面的家庭组织和管理系统，无论是云端使用还是自托管部署，都能帮助你和家人更好地协作和管理日常生活。

Homechart 的核心价值在于统一管理家庭的所有数据——从日程安排、膳食规划、家务分配到预算管理，一切都在同一个平台上进行。它支持多语言界面（包括英文、阿拉伯文、德文、西班牙文、法文、印地文、荷兰文和中文），完全开源透明，无广告骚扰，更重要的是，你的数据永远不会被出售，所有信息都安全加密存储。

无论你的家庭结构如何（核心家庭、重组家庭或离异家庭），Homechart 都能为所有家庭成员提供完美的支持，让每个人都能保持同步，接收关键事件、餐饮和任务的提醒。

项目地址：[点我跳转](https://github.com/candiddev/homechart)

## 部署安装

如果你需要在外网访问你的 Homechart 实例，建议配置内网穿透工具来安全地暴露你的服务。可以考虑使用[帕斯内网穿透](https://passnat.com)这样的服务，先注册账号备用。具体的配置步骤可以参考[内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，让你随时随地都能访问家庭管理平台。

### Docker 单命令快速部署

打开你的 NAS 设备或服务器终端，执行以下命令快速启动 Homechart：

```bash
docker run -d \
  --name homechart \
  -p 8080:3000 \
  -e DATABASE_URL=postgres://user:password@db:5432/homechart \
  -v homechart_data:/data \
  ghcr.io/candiddev/homechart:latest
```

**重要提示**：Homechart 需要 PostgreSQL 数据库支持。如果你没有现成的数据库，建议使用 Docker Compose 方案（见下文）一次性部署完整的数据库和应用。

### Docker Compose 完整部署方案（推荐）

这是最便捷的部署方式，一次启动即可获得完整的 Homechart 环境。

复制下列配置文件，保存为 `docker-compose.yml`：

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    container_name: homechart-db
    environment:
      POSTGRES_USER: homechart
      POSTGRES_PASSWORD: your_secure_password_here
      POSTGRES_DB: homechart
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    restart: unless-stopped

  homechart:
    image: ghcr.io/candiddev/homechart:latest
    container_name: homechart
    depends_on:
      - postgres
    environment:
      DATABASE_URL: postgres://homechart:your_secure_password_here@postgres:5432/homechart
      LOG_LEVEL: info
    ports:
      - "8080:3000"
    volumes:
      - homechart_data:/data
    restart: unless-stopped

volumes:
  postgres_data:
  homechart_data:
```

打开你的飞牛 NAS、群晖或其他 NAS 设备的 Docker 管理界面。

将上述配置文件内容复制到 Docker Compose 编辑器中。**注意**：请务必修改 `POSTGRES\_PASSWORD` 和 `DATABASE\_URL` 中的 `your\_secure\_password\_here` 为你自己的强密码，确保数据库安全。

**端口配置说明**：

容器内部 Homechart 运行在 `3000` 端口

上面的配置将其映射到宿主机的 `8080` 端口

如果 `8080` 端口已被占用，可以修改第一个数字（如改为 `8081:3000`），但容器内的 `3000` 端口保持不变

**存储配置说明**：

`postgres\_data` 卷用于存储数据库文件，确保数据持久化

`homechart\_data` 卷用于存储应用数据

这些卷会自动创建在 Docker 的默认存储位置

**环境变量说明**：

必须设置：

`DATABASE\_URL`：PostgreSQL 数据库连接字符串，格式为 `postgres://用户名:密码@主机:端口/数据库名`

可选设置：

`LOG\_LEVEL`：日志级别，默认为 `info`，可选值包括 `debug`、`info`、`warn`、`error`

其他变量无需额外配置，保持默认即可。

勾选开机自动启动选项，然后点击启动。Docker 会自动下载镜像、创建数据库、启动应用，整个过程通常需要 1-2 分钟。

部署完成后，打开浏览器访问 `http://你的NAS\_IP:8080`（如果你修改了端口，请使用对应的端口号），即可看到 Homechart 的登录页面。首次访问时需要创建账户，之后就可以开始管理你的家庭日程、预算、任务和其他信息了。

## 总结

Homechart 的部署非常简洁高效，特别是通过 Docker Compose，你可以在几分钟内拥有一个完整的家庭管理系统。无论是追踪家务分配、规划家庭预算，还是协调家庭成员的日程，Homechart 都能成为你家庭的数字中枢。开始部署，让你的家庭生活更加有序吧！

---

原文：<https://www.passnat.com/posts/homechart-docker-deployment-guide-nas-self-hosted-household-management>
