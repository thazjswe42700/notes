---
title: "自建私人音乐库服务器，Auddly Music Server 极简部署，内网穿透远程访问享受高品质音乐"
date: 2025-10-16
description: "Auddly Music Server 是功能强大的自建音乐服务器，支持私人音乐库管理、流媒体播放和歌手信息展示。集成Spotify和Last.fm，获取歌手照片、音乐标签、相似推荐。采用Docker快速部署，适合音乐爱好者和家庭媒体中心搭建者享受高品质音乐体验。"
canonical: https://www.passnat.com/posts/self-hosted-private-music-server-auddly-music-server-minimal-deployment-internal-network-penetration-remote-access-high-quality-music
---

## 项目介绍

Auddly Music Server 是一个功能强大的自建音乐服务器，让你能够搭建属于自己的私人音乐库。它提供了完整的音乐管理、流媒体播放、歌手信息展示等功能，支持与 Spotify 和 Last.fm 的集成，帮助你获取歌手照片、音乐标签、相似歌手推荐等丰富的音乐信息。

无论是音乐爱好者还是家庭媒体中心的搭建者，Auddly 都能让你随时随地在自己的设备上享受高品质的音乐体验。项目采用现代化的技术栈构建，部署简单，功能完整。

项目地址： [点我跳转](https://github.com/Wellenline/auddly-server)

## 部署安装

如果你想在外网也能访问你的音乐服务器，建议配置内网穿透功能。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，详细的操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透完整使用教程。

### 使用 Docker-Compose 快速部署

复制下列的配置文件到你的 NAS 或服务器上，新建一个 `docker-compose.yml` 文件：

```yaml
version: '3'
services:
  app:
    container_name: auddly-server
    restart: always
    image: wellenline/auddly-server:latest
    environment:
      - MONGO_URL=mongodb://auddly:SUPER_SECRET_PASSWORD@mongodb/auddly?authSource=admin
      - MUSIC_PATH=/music
      - CACHE_PATH=/cache
      - SPOTIFY_ID=YOUR_SPOTIFY_ID
      - SPOTIFY_SECRET=YOUR_SPOTIFY_SECRET
      - LAST_FM_API_KEY=YOUR_LAST_FM_API_KEY
      - LAST_FM_API_SECRET=YOUR_LAST_FM_API_SECRET
      - LAST_FM_USERNAME=YOUR_LAST_FM_USERNAME
      - LAST_FM_PASSWORD=YOUR_LAST_FM_PASSWORD
      - PORT=5000
      - HOST=http://127.0.0.1:5000
      - ADMIN_EMAIL=YOUR_ADMIN_EMAIL
      - ADMIN_PASSWORD=YOUR_ADMIN_PASSWORD
      - JWT_SECRET=YOUR_JWT_SECRET
    volumes:
      - ./music:/music
      - ./cache:/cache
    ports:
      - 5000:5000
    links:
      - mongodb
    depends_on:
      - mongodb
  
  mongodb:
    image: mongo:latest
    container_name: auddly-mongo
    environment:
      - MONGO_INITDB_ROOT_USERNAME=auddly
      - MONGO_INITDB_ROOT_PASSWORD=SUPER_SECRET_PASSWORD
    volumes:
      - ./data/mongo:/data/db
    ports:
      - 27018:27017
    command: mongod --auth
```

打开你的 NAS 设备或服务器的 Docker 管理界面，找到 Docker-Compose 选项，将上述配置文件粘贴进去。

**重要配置说明：**

这里需要修改的关键信息包括：

1. **SUPER\_SECRET\_PASSWORD** - 数据库密码，请修改为你自己的强密码

2. **ADMIN\_EMAIL 和 ADMIN\_PASSWORD** - 管理员账号和密码，用于登录音乐服务器

3. **JWT\_SECRET** - 安全密钥，可以随意设置一个复杂的字符串

**可选配置（用于增强功能）：**

如果你想要显示歌手照片和获取音乐信息，需要配置以下项：

- **SPOTIFY\_ID 和 SPOTIFY\_SECRET** - 前往 [Spotify Developer](https://developer.spotify.com/dashboard/login) 创建应用获取

- **LAST\_FM\_API\_KEY、LAST\_FM\_API\_SECRET、LAST\_FM\_USERNAME、LAST\_FM\_PASSWORD** - 前往 [Last.fm API](https://www.last.fm/api/account/create) 创建应用获取

其他环境变量保持默认即可。

**存储位置配置：**

- **./music** - 这是你音乐文件的存储位置，请提前在服务器上创建一个文件夹，并将你的音乐文件放入其中

- **./cache** - 用于缓存专辑封面和转码音频，系统会自动管理

- **./data/mongo** - 数据库数据存储位置，请确保该目录存在

**端口说明：**

- 容器内部使用 **5000** 端口（这是 Auddly 服务运行的端口）

- 主机上映射到 **5000** 端口，如果该端口已被占用，可修改为其他端口，例如 **5001:5000**

- MongoDB 数据库使用 **27018** 端口（仅供内部通信，无需修改）

配置完成后，点击启动按钮，等待容器启动完成。

### 访问你的音乐服务器

部署成功后，打开浏览器访问 **http://你的服务器IP:5000**，使用你设置的管理员邮箱和密码登录，即可开始享受你的私人音乐库了。

系统会自动扫描 **/music** 目录下的所有音乐文件，并为你生成完整的音乐库。你可以浏览专辑、歌手、播放列表，搜索你喜爱的歌曲，还能与 Spotify 和 Last.fm 联动获取更多音乐信息。

**到这里部署就完成了！**

通过 Auddly Music Server，你现在拥有了一个完全属于自己的音乐中心。无论是在家中还是通过内网穿透在外网访问，都能随时随地享受你精心收藏的音乐。祝你使用愉快！

本篇教程结束。

---

原文：<https://www.passnat.com/posts/self-hosted-private-music-server-auddly-music-server-minimal-deployment-internal-network-penetration-remote-access-high-quality-music>
