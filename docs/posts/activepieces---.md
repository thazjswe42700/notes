---
title: "开源自动化工作流平台 Activepieces 内网穿透部署完整指南及应用教程"
date: 2025-10-16
description: "Activepieces是开源自动化工作流平台，280+集成模块，支持Google Sheets、OpenAI、Discord等服务。高度可扩展架构，60%社区贡献集成，可视化界面轻松构建复杂自动化流程，实现数据无缝流转和智能协作。"
canonical: https://www.passnat.com/posts/activepieces---
---

## 项目介绍

Activepieces 是一款开源的自动化工作流平台，是 Zapier 的完美替代品。它采用 TypeScript 构建，拥有超过 280+ 个开箱即用的集成模块，支持 Google Sheets、OpenAI、Discord、RSS 等众多流行服务。

这个平台的核心优势在于其高度可扩展的架构设计。所有集成模块都是开源的，60% 以上由社区贡献，你可以轻松创建自己的自定义集成。无论是技术专家还是非技术用户，都能通过直观的可视化界面快速上手，构建复杂的自动化流程，实现数据的无缝流转和智能协作。

项目地址：[点我跳转](https://github.com/activepieces/activepieces)

## 部署安装

如果你需要在公网环境中访问部署的 Activepieces 平台，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，然后参考 [这份详细攻略](https://www.passnat.com/posts/p) 来快速上手各类设备的穿透配置。

### 使用 Docker Compose 快速部署

这是最推荐的部署方式，一条命令即可启动完整的 Activepieces 环境。

### 第一步：创建配置文件

在你的 NAS 或服务器上创建一个部署目录，然后将以下配置文件保存为 `docker-compose.yml`：

```yaml
version: '3.0'
services:
  activepieces:
    image: ghcr.io/activepieces/activepieces:0.70.6
    container_name: activepieces
    restart: unless-stopped
    ports:
      - '8080:80'
    depends_on:
      - postgres
      - redis
    env_file: .env
    volumes:
      - ./cache:/usr/src/app/cache
    networks:
      - activepieces
  postgres:
    image: 'postgres:14.4'
    container_name: postgres
    restart: unless-stopped
    env_file: .env
    environment:
      - 'POSTGRES_DB=${AP_POSTGRES_DATABASE}'
      - 'POSTGRES_PASSWORD=${AP_POSTGRES_PASSWORD}'
      - 'POSTGRES_USER=${AP_POSTGRES_USERNAME}'
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - activepieces
  redis:
    image: 'redis:7.0.7'
    container_name: redis
    restart: unless-stopped
    volumes:
      - 'redis_data:/data'
    networks:
      - activepieces
volumes:
  postgres_data:
  redis_data:
networks:
  activepieces:
```

### 第二步：创建环境配置文件

在同一目录下创建 `.env` 文件，用于配置数据库和应用参数：

```
# 数据库配置（必须设置）
AP_POSTGRES_DATABASE=activepieces
AP_POSTGRES_PASSWORD=your_secure_password_here
AP_POSTGRES_USERNAME=activepieces

# 应用配置（可选）
AP_ENVIRONMENT=prod
AP_LOG_LEVEL=info
```

### 重要提示

请务必将 `your\_secure\_password\_here` 替换为一个强密码，这是保护你的数据库安全的关键。

### 第三步：启动服务

打开你的 NAS 管理界面（如飞牛 NAS、群晖等），进入 Docker 管理模块，上传上述两个配置文件到部署目录。然后执行以下命令启动所有服务：

```
docker-compose up -d
```

### 第四步：端口配置说明

容器内部应用运行在 80 端口
宿主机映射到 8080 端口
如果你的宿主机 8080 端口已被占用，可以修改 `docker-compose.yml` 中的 `ports` 配置。例如，改为 `'9090:80'` 即可将服务映射到 9090 端口。只需修改冒号前面的数字，后面的 80 保持不变。

### 第五步：数据持久化

该配置已包含两个数据卷：
`postgres\_data`：用于存储 PostgreSQL 数据库文件
`redis\_data`：用于存储 Redis 缓存数据
`./cache`：应用缓存目录
这些目录会自动创建和管理，无需手动干预。

### 第六步：访问应用

等待容器完全启动（通常需要 30-60 秒），然后在浏览器中访问：

```
http://你的服务器IP:8080
```

如果一切正常，你将看到 Activepieces 的登录界面。首次访问时，系统会引导你完成初始化设置。

### 第七步：开机自启配置

如果你希望 NAS 重启后自动启动 Activepieces，在 Docker 管理界面中勾选容器的'开机自启'选项即可。

## 总结

通过以上步骤，你已经成功部署了一个完整的 Activepieces 自动化平台。这个平台现在可以帮助你实现各种工作流自动化任务，连接不同的服务和应用，大幅提升工作效率。如遇到任何问题，可以查阅 [官方文档](https://www.activepieces.com/docs) 或在 [Discord 社区](https://discord.gg/2jUXBKDdP8) 寻求帮助。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/activepieces--->
