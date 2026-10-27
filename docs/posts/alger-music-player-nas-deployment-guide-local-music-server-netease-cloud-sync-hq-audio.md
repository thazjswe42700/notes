---
title: "Alger Music Player NAS部署指南，本地音乐服务器搭建，网易云账号同步下载高品质音乐"
date: 2025-10-16
description: "Alger Music Player是功能强大的第三方音乐播放器，支持Desktop、Web、Mobile等全平台。提供网易云登录、歌词显示、音乐下载、EQ均衡器等专业功能。本指南详细说明Docker快速部署到NAS的步骤，配置端口映射和存储目录，轻松在家庭网络享受高品质音乐体验。"
canonical: https://www.passnat.com/posts/alger-music-player-nas-deployment-guide-local-music-server-netease-cloud-sync-hq-audio
---

Alger Music Player 是一款功能强大的第三方音乐播放器，集本地服务、桌面歌词、音乐下载等多种功能于一身。这是一个全平台适配的音乐解决方案，支持 Desktop、Web、Mobile Web 等多个平台，让你随时随地享受高品质音乐体验。

项目采用本地化服务架构，无需依赖在线 API，基于 netease-cloud-music-api 构建，提供网易云账号登录与同步、音乐推荐、歌单管理、MV 排行榜等丰富功能。支持 EQ 均衡器、定时播放、倍速播放、高品质音乐下载等专业级音乐功能，同时提供沉浸式歌词显示、独立桌面歌词窗口、明暗主题切换等优秀的交互体验。

项目地址：[点我跳转](https://github.com/algerkong/AlgerMusicPlayer)

## 部署安装

如果你想在家庭网络外也能访问这个音乐播放器，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的详细操作步骤可以参考 [全平台内网穿透上手指南](https://www.passnat.com/posts/p)。

### Docker 快速部署

打开你的 NAS 设备（飞牛 NAS 或其他 Docker 支持的 NAS）

点击本地镜像 - 添加镜像 - 从 URL 添加，在镜像字段填入：

```
algerkong/alger-music-player:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

**容器配置说明：**

勾选开机自动启动，然后进行端口映射配置。项目默认使用以下端口：

3000 - Web 应用访问端口（容器内部端口）

8080 - 本地服务 API 端口（容器内部端口）

如果你的 NAS 上这些端口已被占用，可以修改宿主机端口（冒号左侧的数字），但容器内端口保持不变即可。例如将 3000:3000 改为 3001:3000。

**存储位置配置：**

如需持久化保存播放历史、收藏和下载的音乐文件，建议创建以下文件夹映射：

/app/data - 应用数据存储目录（包含播放记录、收藏等）

/app/downloads - 音乐下载目录

在 NAS 中创建对应的文件夹后，将其映射到容器内的这些路径。

**环境变量设置：**

通常情况下，Alger Music Player 无需额外的环境变量配置，使用默认设置即可正常运行。如有特殊需求，可根据项目文档进行自定义配置。

完成上述配置后，点击启动容器。稍待片刻，容器启动完成。

### 访问应用

部署完成后，在浏览器中访问：

```
http://你的NAS_IP:3000
```

即可打开 Alger Music Player 的 Web 界面。首次使用时，使用你的网易云账号登录，即可同步你的收藏、歌单和播放历史。

## 总结

通过上述步骤，你已经成功在 NAS 上部署了 Alger Music Player。这个强大的音乐播放器现在已经为你的家庭网络服务。你可以在任何设备上通过浏览器访问它，享受高品质音乐、管理歌单、下载喜爱的歌曲。配合内网穿透工具，你还可以在外出时随时访问自己的音乐库。开始你的音乐之旅吧！

---

原文：<https://www.passnat.com/posts/alger-music-player-nas-deployment-guide-local-music-server-netease-cloud-sync-hq-audio>
