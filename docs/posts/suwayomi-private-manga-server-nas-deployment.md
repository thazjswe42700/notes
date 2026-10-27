---
title: "自建私人漫画库 Suwayomi 服务器，无限漫画源在线阅读，NAS 一键部署内网穿透远程访问"
date: 2025-10-16
description: "Suwayomi是开源漫画阅读服务器，基于Mihon扩展生态构建。完全免费跨平台，支持Windows、macOS、Linux和NAS部署。拥有强大扩展系统，可安装数百个漫画源，支持自动更新、离线下载、收藏管理、进度同步。界面简洁易用，提供多个前端选择，支持多设备联动阅读，让你随时享受优质漫画体验。"
canonical: https://www.passnat.com/posts/suwayomi-private-manga-server-nas-deployment
---

## 项目介绍

Suwayomi 是一个开源的漫画阅读服务器，基于 Mihon（原 Tachiyomi）的扩展生态构建。这是一个完全免费、跨平台的解决方案，支持 Windows、macOS、Linux 等多种操作系统，甚至可以在 NAS 上部署运行。

该项目的核心特色是拥有强大的扩展系统，你可以安装数百个来自 Mihon 社区的漫画源扩展，轻松搜索、浏览和阅读来自全球各地的漫画资源。支持自动更新检查、离线下载、收藏管理、阅读进度同步等功能，让你随时随地享受优质的漫画阅读体验。

界面简洁易用，提供了多个前端选择（WebUI、VUI 等），完全不需要自己配置复杂的资源源。支持与 Mihon 和 Neko 等应用进行同步，实现多设备联动阅读。

项目地址：[点我跳转](https://github.com/Suwayomi/Tachidesk-Server)

## 部署安装

如需在外网也能打开项目，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。详细的操作步骤可以参考 [帕斯内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，一键配置即可轻松实现远程访问。

### 快速部署（Docker 方式）

打开你的 NAS 设备或任何支持 Docker 的系统，执行以下命令快速启动 Suwayomi：

```bash
docker run -d \
  --name suwayomi \
  -p 4567:4567 \
  -v suwayomi_data:/home/suwayomi/.local/share/Tachidesk \
  --restart unless-stopped \
  ghcr.io/suwayomi/suwayomi-server:latest
```

如果你的 NAS 设备提供了 Docker 管理界面，也可以通过 Web 界面添加镜像。选择'从 URL 添加'，输入镜像地址：`ghcr.io/suwayomi/suwayomi-server:latest`，等待下载完成后点击启动即可。

### 使用 Docker Compose（推荐）

如果你更倾向于使用 Docker Compose 来管理容器，可以创建一个 `docker-compose.yml` 文件：

```yaml
version: '3.8'

services:
  suwayomi:
    image: ghcr.io/suwayomi/suwayomi-server:latest
    container_name: suwayomi
    environment:
      - TZ=Asia/Shanghai
      - FLARESOLVERR_ENABLED=false
    volumes:
      - suwayomi_data:/home/suwayomi/.local/share/Tachidesk
    ports:
      - "4567:4567"
    restart: unless-stopped

volumes:
  suwayomi_data:
```

将上述内容保存到文件后，在该文件所在目录执行：

```
docker-compose up -d
```

容器将在后台启动并自动开机启动。

### 端口配置说明

Suwayomi 默认使用 **4567** 端口。如果你的系统中该端口已被占用，可以修改映射配置：

在 Docker 命令中：`-p 你的端口:4567`
在 docker-compose 中：修改 `ports` 下的 `'你的端口:4567'`

注意不要修改冒号后面的 4567，这是容器内部的端口，需要保持不变。

### 数据持久化

Suwayomi 的所有数据（包括漫画库、下载内容、阅读进度等）都存储在 `/home/suwayomi/.local/share/Tachidesk` 目录。通过 `-v suwayomi\_data:/home/suwayomi/.local/share/Tachidesk` 的映射，确保容器重启或更新后数据不会丢失。

### 环境变量配置

以下是常用的环境变量配置说明：

**必须配置的变量：**

无（默认配置即可正常运行）

**可选配置的变量：**

`TZ`：时区设置，默认为 `Etc/UTC`，建议改为 `Asia/Shanghai`（中国时区）
`BIND\_PORT`：服务端口，默认 `4567`
`FLARESOLVERR\_ENABLED`：是否启用 Cloudflare 防护绕过，默认 `false`
`AUTH\_MODE`：认证模式，可设置为 `basic\_auth` 启用用户名密码保护
`AUTH\_USERNAME`：认证用户名（启用 AUTH\_MODE 时需要）
`AUTH\_PASSWORD`：认证密码（启用 AUTH\_MODE 时需要）

其他变量保持默认即可，无需特殊配置。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的设备IP:4567
```

例如：`http://192.168.1.100:4567`

即可打开 Suwayomi 的 Web 界面。首次访问会看到一个简洁的漫画阅读平台，你可以开始安装喜欢的漫画源扩展，搜索和阅读漫画了。

### 部署完成

到此为止，Suwayomi 已经成功部署完毕！你现在拥有了一个私人的漫画阅读服务器，支持在家中任何设备上访问，也可以通过内网穿透在外网随时随地阅读。

本篇教程结束，祝你使用愉快！如有问题可以加入官方 Discord 社区寻求帮助。

---

原文：<https://www.passnat.com/posts/suwayomi-private-manga-server-nas-deployment>
