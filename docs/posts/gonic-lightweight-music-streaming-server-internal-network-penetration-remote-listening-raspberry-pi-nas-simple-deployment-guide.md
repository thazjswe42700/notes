---
title: "轻量级音乐流媒体服务器Gonic，内网穿透远程听歌，树莓派NAS极简部署指南"
date: 2025-10-16
description: "Gonic是一个开源音乐流媒体服务器，完全兼容Subsonic API，采用Go语言编写，资源占用极低，特别适合在树莓派等嵌入式设备上运行。支持音乐库浏览、实时转码、播客、用户管理、Last.fm统计等功能，让你轻松搭建私人音乐平台，随时随地享受音乐收藏。"
canonical: https://www.passnat.com/posts/gonic-lightweight-music-streaming-server-internal-network-penetration-remote-listening-raspberry-pi-nas-simple-deployment-guide
---

## Gonic - 轻量级音乐流媒体服务器

### 项目介绍

Gonic 是一个完全兼容 Subsonic API 的开源音乐流媒体服务器实现。它采用 Go 语言编写，具有极低的资源占用，特别适合在树莓派等嵌入式设备上运行。

这个项目支持通过文件夹或音乐标签浏览你的音乐库，提供了实时音乐转码、播客支持、用户管理、Last.fm/ListenBrainz 音乐统计等功能。无论你是音乐发烧友还是普通听众，都能通过 Gonic 搭建属于自己的私人音乐流媒体平台，随时随地享受你的音乐收藏。

项目地址：[点我跳转](https://github.com/sentriz/gonic)

### 部署安装

想要在外网也能访问你的音乐服务器？推荐使用 [帕斯内网穿透](https://passnat.com) 来实现。先注册好账号，然后参考 [这份完整攻略](https://www.passnat.com/posts/p) 快速上手，轻松将本地服务暴露到互联网。

#### Docker 快速部署

打开你的 NAS 设备或 Docker 管理界面，执行以下命令即可一键启动 Gonic：

```bash
docker run -d \
  --name gonic \
  -p 4747:4747 \
  -v /path/to/music:/music \
  -v /path/to/cache:/cache \
  -v /path/to/db:/db \
  -e GONIC_MUSIC_PATH=/music \
  -e GONIC_CACHE_PATH=/cache \
  -e GONIC_DB_PATH=/db/gonic.db \
  sentriz/gonic:latest
```

**重要提示**：请将上述命令中的路径替换为你实际的音乐文件夹位置。

#### 或者使用飞牛 NAS 部署

1. 打开飞牛 NAS 管理后台

2. 进入 **本地镜像** → **添加镜像** → **从 URL 添加**

3. 镜像地址填写：sentriz/gonic:latest

4. 等待镜像下载完成后，点击启动按钮

#### 容器配置说明

**端口映射**：

容器内部端口：4747（Gonic 服务端口）

如果你的 NAS 上已有其他服务占用 4747 端口，可以修改为其他端口，例如 4748:4747

**存储挂载**：

你需要创建以下三个文件夹并进行映射：

文件夹用途 | 说明

/music | 放置你的音乐文件的目录

/cache | 用于存储转码后的音频和封面缓存

/db | 数据库文件存储位置

**环境变量设置**

必须设置：

GONIC\_MUSIC\_PATH：你的音乐文件夹路径（容器内路径，例如 /music）

GONIC\_CACHE\_PATH：缓存文件夹路径（例如 /cache）

GONIC\_DB\_PATH：数据库文件完整路径（例如 /db/gonic.db）

可选设置（根据需要）：

GONIC\_LISTEN\_ADDR：监听地址和端口，默认 0.0.0.0:4747

GONIC\_SCAN\_INTERVAL：自动扫描音乐库的间隔时间（分钟），不设置则禁用自动扫描

GONIC\_SCAN\_AT\_START\_ENABLED：启动时是否进行初始扫描，默认不启用

GONIC\_PODCAST\_PATH：播客文件夹路径（如需要播客功能）

GONIC\_JUKEBOX\_ENABLED：是否启用点唱机模式（服务器端播放）

其他环境变量都可以保持默认，无需特别配置。

#### 访问你的音乐服务器

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:4747
```

默认登录账号：**admin**

默认登录密码：**admin**

登录后，你可以在设置界面修改密码、配置 Last.fm 账号、管理用户等。

### 使用建议

首次使用：登录后建议立即修改默认密码，确保账号安全

音乐扫描：首次扫描可能需要一段时间（取决于你的音乐库大小），耐心等待即可

客户端支持：Gonic 兼容多种 Subsonic 客户端，包括 Symfonium、Ultrasonic、Airsonic 等

转码功能：如需启用音频转码，需要在容器中安装 FFmpeg

本篇教程到此结束，祝你享受属于自己的私人音乐流媒体服务！

---

原文：<https://www.passnat.com/posts/gonic-lightweight-music-streaming-server-internal-network-penetration-remote-listening-raspberry-pi-nas-simple-deployment-guide>
