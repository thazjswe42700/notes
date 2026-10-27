---
title: "现代React推箱子游戏，500+谜题无限撤销，NAS极简部署内网穿透在线玩"
date: 2025-10-16
description: "Sokoban 是采用 React 18 + TypeScript 打造的经典益智游戏克隆版，包含近 500 个推箱子谜题，支持无限次撤销操作。提供简洁直观的游戏界面，支持 Docker 快速部署到服务器、NAS 等设备，让你随时随地享受流畅的益智游戏体验。"
canonical: https://www.passnat.com/posts/modern-react-sokoban-game-500-puzzles-unlimited-undo-nas-simple-deployment-intranet-penetration-online-play
---

## 项目介绍

Sokoban 是一款经典的益智游戏克隆版本，采用现代化的 React 18 + TypeScript 技术栈打造。这个项目包含了近 500 个推箱子谜题，支持无限次撤销操作，让你可以尽情享受这款传统益智游戏的乐趣。

无论是想要打发闲暇时光，还是挑战自己的逻辑思维能力，Sokoban 都能为你提供一个流畅、响应迅速的游戏体验。界面简洁直观，上手即玩，无需复杂配置。

项目地址：[点我跳转](https://github.com/ecyrbe/sokoban)

## 部署安装

如果你想在自己的服务器或 NAS 上部署这个项目，并在外网访问，建议配置内网穿透工具。可以前往[帕斯内网穿透](https://passnat.com)注册账号，详细的跨平台部署指南请[点击这里查看](https://www.passnat.com/posts/p)。

### Docker 快速启动

这是最简便的部署方式，只需一条命令即可启动：

```bash
docker run -d -p 3000:3000 --name sokoban node:18-alpine sh -c "git clone https://github.com/ecyrbe/sokoban.git /app && cd /app && npm install && npm start"
```

### NAS 设备部署步骤

打开你的飞牛 NAS 或其他 NAS 设备

点击**本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：node:18-alpine

等待镜像下载完成后，点击**启动**按钮启动容器

在启动配置中进行以下设置：

#### 端口映射设置

容器内部端口：3000

宿主机端口：3000（如果该端口被占用，可修改为其他端口如3001、3002等，但容器内部端口保持3000不变）

#### 启动命令

在启动命令中填入以下内容：

```
sh -c "git clone https://github.com/ecyrbe/sokoban.git /app && cd /app && npm install && npm start"
```

#### 存储配置

此项目无需持久化存储配置，默认设置即可

#### 其他设置

勾选**开机自启动**以确保容器在 NAS 重启后自动启动

其他环境变量无需配置，保持默认即可

点击**启动**按钮完成部署

## 访问应用

部署完成后，打开浏览器访问：

```
http://你的NAS_IP:3000
```

稍等片刻，Sokoban 游戏界面就会加载完成，你可以立即开始游戏了！

本篇教程结束

---

原文：<https://www.passnat.com/posts/modern-react-sokoban-game-500-puzzles-unlimited-undo-nas-simple-deployment-intranet-penetration-online-play>
