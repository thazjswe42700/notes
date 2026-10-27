---
title: "开源益智游戏鱼了个鱼Docker部署指南，NAS一键启动无广告通关体验"
date: 2025-10-16
description: "鱼了个鱼是一款纯前端益智游戏，灵感源自羊了个羊。采用TypeScript+Vue+Vite开发，提供4种难度、自定义关卡、无限道具等功能。支持Docker一键部署到NAS设备，无需广告即可畅玩。完整的游戏体验，尽在指尖。"
canonical: https://www.passnat.com/posts/open-source-puzzle-game-fish-docker-deployment-guide-nas-one-click-launch-ad-free-experience
---

## 项目介绍

**鱼了个鱼**是一款纯前端实现的益智游戏，灵感来自于火爆的'羊了个羊'。这是一个开源项目，采用 TypeScript + Vue + Vite 技术栈开发，提供了完整的游戏体验，包括多难度选择、自定义关卡、自定义图案以及无限道具使用等丰富功能。

项目不仅支持 4 种预设难度，还允许玩家自定义游戏难度和动物图案（例如 🐔🏀 等表情符号），让每位玩家都能根据自己的喜好打造独特的游戏体验。最棒的是，游戏完全不需要观看广告，并且能够真正通关，让玩家享受到纯粹的游戏乐趣。

项目地址：[点我跳转](https://github.com/liyupi/yulegeyu)

## 部署安装

若想在外网也能随时体验这款游戏，建议配置内网穿透功能。你可以访问 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地访问你的本地服务。具体的部署步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透完整上手指南。

### Docker 单命令部署

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理面板。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**。

在镜像地址栏填入以下地址：

```
liyupi/yulegeyu:latest
```

等待镜像下载完成，下载成功后点击 **启动按钮** 启动容器。

### 容器配置说明

**开机自动启动**：勾选此选项，确保 NAS 重启后容器能自动运行。

**端口配置**：

容器内部端口：`3000`

你可以自定义宿主机端口（例如设置为 `8080`），如果与其他服务冲突可以修改宿主机端口号，但容器内的 `3000` 端口保持不变

**存储配置**：

这个项目是纯前端应用，无需特殊的数据持久化目录配置，保持默认设置即可。

**环境变量**：

该项目无需额外配置环境变量，直接点击下一步启动即可。

### 访问游戏

容器启动成功后，在浏览器中访问：

```
http://你的NAS地址:3000
```

例如：`http://192.168.1.100:3000`

此时你就能看到鱼了个鱼的游戏界面，开始体验这款有趣的益智游戏了！

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-puzzle-game-fish-docker-deployment-guide-nas-one-click-launch-ad-free-experience>


## 相关文章

- [现代React推箱子游戏，500+谜题无限撤销，NAS极简部署内网穿透在线玩](modern-react-sokoban-game-500-puzzles-unlimited-undo-nas-simple-deployment-intranet-penetration-online-play.md)
