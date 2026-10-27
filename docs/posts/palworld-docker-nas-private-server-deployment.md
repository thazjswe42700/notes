---
title: "Palworld Docker 专用服务器快速部署指南，NAS 一键搭建私人游戏服务器"
date: 2025-10-16
description: "探索 Palworld Docker 服务器搭建完全指南。了解如何在 Linux 和 Docker 上快速部署私人游戏服务器，支持 Steam 和 Xbox 平台。包含系统要求、端口配置、环境变量设置、自动备份、RCON 远程控制等详细步骤，轻松与朋友建立联机游戏环境。"
canonical: https://www.passnat.com/posts/palworld-docker-nas-private-server-deployment
---

## 项目介绍

Palworld 是一款风靡全球的多人生存冒险游戏，而这个 Docker 项目为你提供了一个开箱即用的 Palworld 专用服务器解决方案。基于 Linux 和 Docker 构建，支持 Steam 和 Xbox 平台连接，无论你是想与朋友建立私人服务器，还是搭建大型联机游戏环境，这个项目都能轻松胜任。

项目完全开源，提供了完整的配置管理、自动备份、RCON 远程控制、Webhook 集成等强大功能。最重要的是，你可以完全掌控自己的游戏服务器，不受任何第三方限制，随时随地与朋友们一起捕捉、饲养和战斗！

项目地址：[点我跳转](https://github.com/jammsen/docker-palworld-dedicated-server)

## 部署安装

如需在外网也能访问你的服务器，需要配置内网穿透。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号。具体的操作步骤可以 [参考这里](https://www.passnat.com/posts/p)，了解全平台内网穿透的快速上手方法。

### 系统要求

在开始部署前，请确保你的服务器满足以下配置：

1-8 玩家：4 核 CPU（高主频）+ 8GB 内存 + 30GB 存储

8-12+ 玩家：6-8 核 CPU（高主频）+ 12GB 内存 + 30GB+ 存储

### 快速部署

打开你的 NAS 设备或服务器的 Docker 管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**。

在镜像地址栏填入：jammsen/palworld-dedicated-server:latest

等待镜像下载完成后，点击 **启动** 按钮启动容器。

勾选 **开机自动启动** 后进入下一步。

### 端口配置

在端口设置中，需要映射以下端口（容器内部端口保持不变）：

8211/udp：游戏服务器主端口（必须）

27015/tcp：RCON 远程控制端口（可选，用于服务器管理）

如果你的服务器上这些端口已被占用，可以修改宿主机的映射端口。例如，如果 8211 被占用，可以映射为 18211，但容器内部端口必须保持为 8211。

### 存储位置

在存储配置中，你需要创建一个专用的游戏数据目录用来存放服务器文件、配置和存档。

建议在你的 NAS 或服务器上创建一个目录（例如 /srv/palworld 或 /home/username/palworld），然后将其映射到容器内的 /palworld 路径。这样可以确保你的游戏数据和存档不会因为容器重启而丢失。

### 环境变量设置

**必须设置：**

PUID：运行容器的用户 ID（推荐设置为 1000，不能为 0）

PGID：运行容器的用户组 ID（推荐设置为 1000，不能为 0）

SERVER\_NAME：你的服务器名称（玩家看到的服务器名字）

SERVER\_DESCRIPTION：服务器描述（可选但推荐填写）

ADMIN\_PASSWORD：管理员密码（用于 RCON 控制）

**可选设置：**

SERVER\_PASSWORD：服务器连接密码（留空则无密码）

MAX\_PLAYERS：最大玩家数（默认 32）

ALLOW\_CONNECT\_PLATFORM：允许连接的平台（Steam 或 Xbox，默认 Steam）

BACKUP\_ENABLED：是否启用自动备份（true/false）

BACKUP\_CRON\_EXPRESSION：备份时间表（默认每 4 小时备份一次）

WEBHOOK\_ENABLED：是否启用 Webhook 通知（true/false）

WEBHOOK\_URL：Webhook 地址（用于 Discord 通知）

其他配置保持默认即可，直接下一步启动容器。

### 访问你的服务器

创建成功后，你可以在 Palworld 游戏中添加服务器。使用你服务器的 IP 地址和映射的端口号（默认 8211）即可连接。

如果你需要远程管理服务器，可以使用以下命令执行 RCON 命令：

```bash
docker exec palworld-dedicated-server rconcli showplayers
docker exec palworld-dedicated-server rconcli save
docker exec palworld-dedicated-server rconcli info
```

### 备份管理

项目内置了强大的备份功能，你可以手动创建备份或设置自动备份：

```bash
# 创建备份
docker exec -user steam palworld-dedicated-server backup create

# 列出所有备份
docker exec -user steam palworld-dedicated-server backup list

# 清理旧备份（保留最近 3 个）
docker exec -user steam palworld-dedicated-server backup clean 3
```

本篇教程结束。现在你已经拥有了属于自己的 Palworld 专用服务器！与朋友们一起享受这款精彩的游戏吧。🎮😉

---

原文：<https://www.passnat.com/posts/palworld-docker-nas-private-server-deployment>
