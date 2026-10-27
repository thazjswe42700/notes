---
title: "Nextflux现代RSS阅读器，React+Vite快速响应，Docker极简部署Miniflux服务，内网穿透远程访问订阅源"
date: 2025-10-16
description: "Nextflux是现代化RSS阅读器客户端，基于React18+Vite+TailwindCSS构建，专为Miniflux服务器设计。支持自动后台同步、深色浅色主题、多语言国际化、播客视频播放、PWA安装等功能。提供Docker一键部署，快速搭建个人RSS订阅管理系统，高效阅读海量资讯源。"
canonical: https://www.passnat.com/posts/nextflux-modern-rss-reader-react-vite-docker-miniflux
---

## 项目介绍

Nextflux 是一个现代化的 RSS 阅读器客户端，专为 Miniflux 服务器打造。它采用 React 18 + Vite + TailwindCSS 的技术栈构建，提供了快速响应的用户界面和丰富的阅读体验。无论你是资讯爱好者还是内容订阅者，Nextflux 都能帮你高效地管理和阅读海量的 RSS 源。

项目支持自动后台同步、深色/浅色主题切换、多语言国际化（英文、中文、土耳其文、法文）、播客播放、视频播放、图片库展示等功能。同时提供了完整的 PWA 支持，可以像原生应用一样安装在你的设备上，随时随地畅享阅读体验。

项目地址：[点我跳转](https://github.com/electh/nextflux)

## 部署安装

如果你想在办公室或外出时也能访问家中部署的 Nextflux，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它提供了便捷的跨域访问解决方案。具体的详细操作指南可以 [查看这里](https://www.passnat.com/posts/p)，包含全平台的快速上手教程。

### 最快速的部署方式 - Docker 单命令启动

打开你的 NAS 设备或服务器的 Docker 管理面板，执行下面这条命令即可一键启动 Nextflux：

```bash
docker run -d --name nextflux -p 3000:3000 --restart unless-stopped electh/nextflux:latest
```

命令执行完成后，稍等片刻让容器启动完毕。

**端口说明：** 上述命令中的 3000:3000 表示将容器内的 3000 端口映射到主机的 3000 端口。如果你的主机上已经有其他服务占用了 3000 端口，可以修改前面的数字。例如改成 8080:3000 就表示通过主机的 8080 端口来访问应用。

**启动验证：** 容器启动后，打开浏览器访问 http://你的服务器IP:3000（如果修改了端口号，请使用对应的端口），就能看到 Nextflux 的界面了。

**首次配置：** 首次打开应用时，你需要连接到你的 Miniflux 服务器。在设置中输入：

Miniflux 服务器地址

API Token 或用户名/密码

配置完成后就可以开始订阅和阅读 RSS 源了。

### 使用 Docker Compose 部署（包含 Miniflux）

如果你还没有 Miniflux 服务器，也可以使用 Docker Compose 同时部署 Nextflux 和 Miniflux。

在你的 NAS 或服务器上创建一个部署文件夹，然后在该文件夹中创建一个 docker-compose.yml 文件，输入以下内容：

```yaml
version: '3'
services:
  nextflux:
    image: electh/nextflux:latest
    container_name: nextflux
    ports:
      - "3000:3000"
    restart: unless-stopped
    depends_on:
      - miniflux
    environment:
      - MINIFLUX_URL=http://miniflux:8080

  miniflux:
    image: miniflux/miniflux:latest
    container_name: miniflux
    ports:
      - "8080:8080"
    restart: unless-stopped
    environment:
      - DATABASE_URL=postgres://miniflux:miniflux@postgres/miniflux
      - RUN_MIGRATIONS=1
      - CREATE_ADMIN_USER=1
      - ADMIN_USERNAME=admin
      - ADMIN_PASSWORD=admin123

  postgres:
    image: postgres:15-alpine
    container_name: miniflux_db
    restart: unless-stopped
    environment:
      - POSTGRES_DB=miniflux
      - POSTGRES_USER=miniflux
      - POSTGRES_PASSWORD=miniflux
    volumes:
      - miniflux_db:/var/lib/postgresql/data

volumes:
  miniflux_db:
```

**重要提示：** 上面的配置中 ADMIN\_PASSWORD 和 POSTGRES\_PASSWORD 是默认密码，**强烈建议你根据实际需求修改这些密码**，确保系统安全。

然后在该文件夹打开终端或 SSH，执行：

```bash
docker compose up -d
```

等待容器启动完成。

**访问应用：**

Nextflux 访问地址：http://你的服务器IP:3000

Miniflux 访问地址：http://你的服务器IP:8080（默认用户名：admin，密码：admin123，请务必修改）

**存储说明：** 上述配置中，Miniflux 的数据库会持久化存储在 miniflux\_db 卷中，确保重启容器后数据不会丢失。

本篇教程结束。现在你已经拥有了一个功能强大的 RSS 阅读器，可以尽情享受高效的内容订阅体验了！

---

原文：<https://www.passnat.com/posts/nextflux-modern-rss-reader-react-vite-docker-miniflux>
