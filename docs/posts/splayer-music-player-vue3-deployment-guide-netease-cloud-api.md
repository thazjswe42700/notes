---
title: "功能丰富的音乐播放器SPlayer部署指南，Vue3开发支持网页和客户端双端，集成网易云API随时享受高品质音乐体验"
date: 2025-10-16
description: "SPlayer是功能丰富的现代化音乐播放器，采用Vue3+TypeScript+Electron打造，支持网页端和客户端双端部署。集成网易云音乐API，支持账号登录、歌曲下载、云盘管理、本地音乐库、逐字歌词、MV播放等丰富功能。界面简洁优雅，支持Light/Dark/Auto多种主题模式，提供一站式音乐播放体验。"
canonical: https://www.passnat.com/posts/splayer-music-player-vue3-deployment-guide-netease-cloud-api
---

## 项目介绍

SPlayer 是一款功能丰富的音乐播放器，采用 Vue 3 + TypeScript + Naïve UI + Electron 技术栈精心打造。这是一个支持网页端和客户端双端部署的现代化音乐播放解决方案，让你能够随时随地享受高品质的音乐体验。

项目集成了网易云音乐 API，支持账号登录、每日签到、歌曲下载（最高支持 Hi-Res）、云盘管理、本地音乐库、逐字歌词、MV 播放等丰富功能。无论是在线流媒体还是本地音乐管理，SPlayer 都能为你提供一站式的音乐播放体验。界面设计简洁优雅，支持 Light/Dark/Auto 多种主题模式，还能根据封面自适应调整主题色。

项目地址： [点我跳转](https://github.com/imsyy/SPlayer)

## 部署安装

如果你想在外网也能访问到部署在家中的 SPlayer，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的详细操作步骤可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台快速上手教程。

### Docker 单命令部署（推荐）

这是最快速的部署方式，只需一条命令即可启动 SPlayer。

打开你的 NAS 设备或服务器，进入 Docker 管理界面。

点击本地镜像-添加镜像-从 URL 添加，在镜像地址栏填入：

```
imsyy/splayer:latest
```

或者使用 GitHub 的镜像源：

```
ghcr.io/imsyy/splayer:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

在容器启动配置中，进行以下设置：

**端口映射设置：**

将容器内部的 25884 端口映射到主机端口。如果你的 25884 端口已被占用，可以修改为其他端口号（如 8080、8888 等），但容器内部端口需保持为 25884。

例如：主机端口:25884 → 容器内部:25884

**存储与数据持久化：**

SPlayer 支持本地音乐库管理和用户配置保存。建议在 NAS 上创建一个专用文件夹（如 /data/splayer）用于存储音乐和配置文件，将其映射到容器内的相应目录。

**环境变量配置：**

SPlayer 的核心功能需要依赖 NeteaseCloudMusicApi 服务。如果你已部署了该 API 服务，需要设置以下环境变量：

环境变量 | 说明 | 默认值 | 是否必需
---------|------|-------|--------
VITE\_API\_URL | NeteaseCloudMusicApi 的访问地址 | 无 | 必须设置

例如：VITE\_API\_URL=https://your-api-domain.com

其他环境变量保持默认即可，无需额外配置。

勾选开机自动启动选项后，点击下一步启动容器。

创建成功后，在浏览器中访问 http://你的IP:25884（如果修改了端口，请使用对应的端口号），即可看到 SPlayer 的界面。

### Docker Compose 部署

如果你偏好使用 Docker Compose 管理容器，可以复制以下配置文件：

```yaml
version: '3.8'

services:
  splayer:
    image: imsyy/splayer:latest
    container_name: SPlayer
    ports:
      - "25884:25884"
    environment:
      - VITE_API_URL=https://your-api-domain.com
    restart: always
    volumes:
      - ./data:/app/data
```

将上述内容保存为 docker-compose.yml 文件，然后在文件所在目录执行：

```
docker-compose up -d
```

容器将在后台启动，访问 http://localhost:25884 即可使用。

**功能特性速览：**

✨ 支持账号登录与扫码登录
📱 手机号登录支持
📅 自动每日签到及云贝签到
💻 桌面歌词显示
🎨 封面主题色自适应，全站着色支持
🌚 Light / Dark / Auto 多种主题模式
📁 本地歌曲管理、分类与标签编辑
⬇️ 歌曲下载（最高支持 Hi-Res）
❤️ 歌单收藏与歌手关注
🎶 每日推荐与私人 FM
☁️ 云盘音乐上传与播放
📝 逐字歌词与歌词翻译
📹 MV 与视频播放
🔄 PWA 支持
💬 评论区功能
🌐 国际化多语言支持

本篇教程结束

---

原文：<https://www.passnat.com/posts/splayer-music-player-vue3-deployment-guide-netease-cloud-api>


## 相关文章

- [docker-autocompose Docker容器自动生成compose配置文件工具，一键导出容器环境设置](docker-autocompose-container-compose-generator.md)
- [开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问](appsmith-nas-docker-compose-local-deployment-guide.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [轻量级音乐流媒体服务器Gonic，内网穿透远程听歌，树莓派NAS极简部署指南](gonic-lightweight-music-streaming-server-internal-network-penetration-remote-listening-raspberry-pi-nas-simple-deployment-guide.md)
- [网页广播播放器PawTunes部署教程 Docker一键启动NAS内网穿透远程访问](pawtubes-docker-nas-remote-access-setup.md)
