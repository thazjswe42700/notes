---
title: "自托管个人收藏管理系统Koillection，NAS私有部署Docker快速上手指南"
date: 2025-10-16
description: "Koillection 是一款功能强大的自托管个人收藏管理系统，支持自定义元数据管理。无论收集书籍、DVD、邮票还是游戏卡牌，都能有序整理。采用现代化技术栈，提供简洁强大的管理界面。本指南详细介绍 Docker Compose 快速部署方式、配置说明及数据备份要点，帮助用户轻松在 NAS 或服务器上搭建个人收藏库。"
canonical: https://www.passnat.com/posts/koillection-nas-docker-deployment-guide
---

## 项目介绍

Koillection 是一款功能强大的自托管个人收藏管理系统，专为那些热爱收集的用户而设计。无论你收集的是书籍、DVD、邮票、游戏卡牌还是其他珍贵物品，Koillection 都能帮你有序地整理和管理。这是一个完全开源的解决方案，让你可以完全掌控自己的收藏数据，不用担心隐私问题。

该项目支持自定义元数据管理，你可以根据自己的需求灵活配置收藏品的属性信息。项目采用现代化的技术栈构建，提供了简洁而强大的管理界面。无论是个人收藏爱好者还是小型收藏馆，都能通过 Koillection 实现高效的数据管理和展示。

[点我跳转](https://github.com/koillection/koillection)

项目地址：

## 部署安装

如果你希望在家外也能访问你的收藏库，可以考虑配置内网穿透工具来实现远程访问。推荐使用 [帕斯内网穿透](https://passnat.com) 服务，先完成账号注册后即可开始配置。详细的操作步骤可以参考 [全平台内网穿透快速上手指南](https://www.passnat.com/posts/p)，即使是技术小白也能轻松完成设置。

### 使用 Docker Compose 快速部署

这是最推荐的部署方式，适合大多数用户。复制下面的配置文件到你的 NAS 或服务器上：

```yaml
version: '3.8'

services:
  koillection:
    image: benjaminjonard/koillection:latest
    container_name: koillection
    restart: unless-stopped
    ports:
      - "80:80"
    environment:
      - DATABASE_URL=postgresql://koillection:password@db:5432/koillection
      - APP_ENV=prod
      - APP_SECRET=your-secret-key-here
    volumes:
      - koillection_data:/var/www/html
    depends_on:
      - db

  db:
    image: postgres:15-alpine
    container_name: koillection_db
    restart: unless-stopped
    environment:
      - POSTGRES_USER=koillection
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=koillection
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  koillection_data:
  postgres_data:
```

**重要配置说明：**

登录凭证修改：请务必修改上面配置中的数据库密码，将 password 替换为你自己的安全密码。同时修改 APP\_SECRET 为一个强密钥。

端口配置：如果你的服务器上 80 端口已被占用，可以修改 ports 字段。例如改为 '8080:80' 表示外部访问使用 8080 端口，但容器内部仍使用 80 端口。

数据持久化：配置文件中已经配置了两个数据卷：

koillection\_data：用于存储应用程序文件

postgres\_data：用于存储数据库数据

环境变量说明：

DATABASE\_URL：数据库连接字符串（必须设置）

APP\_ENV：应用环境，生产环境设置为 prod

APP\_SECRET：应用密钥，用于加密会话数据（必须设置）

其他变量均为可选配置

**部署步骤：**

打开你的飞牛 NAS 或其他 NAS 设备，进入 Docker 管理界面。将上述 docker-compose.yml 文件内容保存到你的 NAS 上，然后通过 Docker Compose 启动服务。启动后，系统会自动创建必要的数据库和应用容器。

勾选'开机自启动'选项，确保 NAS 重启后服务能够自动恢复。完成配置后点击启动，等待容器初始化完成。

### 数据备份提示

**重要警告**：在更新 Koillection 到新版本前，请务必备份你的数据库。虽然开发者已尽力测试各个版本，特别是包含数据迁移的更新，但某些边界情况仍可能导致数据问题。定期备份是保护你的收藏数据的最佳实践。

### 访问应用

部署完成后，在浏览器中访问 http://你的服务器IP:80（如果修改了端口，请使用对应的端口号）即可打开 Koillection 管理界面。首次访问时，系统会引导你完成初始化设置。

## 总结

通过上述步骤，你就能在自己的 NAS 或服务器上成功部署 Koillection，开始管理你的各类收藏品。这个自托管的解决方案让你完全掌控数据，同时享受现代化的管理体验。如果遇到任何问题，可以查阅项目的 Wiki 文档或在 GitHub 上提交 Issue。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/koillection-nas-docker-deployment-guide>
