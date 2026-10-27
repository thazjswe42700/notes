---
title: "自建音乐库跨平台播放器Feishin完整部署指南，内网穿透远程访问方案"
date: 2025-10-16
description: "探索Feishin跨平台音乐播放器：支持Navidrome、Jellyfin等多种后端，提供Docker一键部署、现代化界面设计，包含歌词同步、智能歌单等丰富功能。本指南详细讲解Docker单命令启动、NAS部署、Docker Compose配置及环境变量设置，助你快速搭建个人音乐流媒体服务。"
canonical: https://www.passnat.com/posts/feishin-music-player-deploy-guide-lan-tunneling
---

## Feishin - 跨平台音乐播放器完全部署指南

### 项目介绍

Feishin 是一款功能强大的跨平台音乐流媒体播放器，专为音乐爱好者精心打造。它是对经典项目 Sonixd 的全新重写，集现代化设计与强大功能于一身。

这款播放器支持多种音乐服务器后端，包括 Navidrome、Jellyfin、Airsonic-Advanced、Ampache、Gonic 等 OpenSubsonic 兼容的服务器。无论你使用哪种音乐服务，Feishin 都能完美适配。

Feishin 提供了两种强大的播放引擎：MPV 播放器后端和 Web 播放器后端，让你在不同场景下都有最佳的音乐体验。界面采用现代化设计，操作直观流畅。支持歌词同步、播放记录、智能歌单编辑、播放到服务器等丰富功能，让你可以随时随地享受高质量的音乐。

项目地址：[点我跳转](https://github.com/jeffvli/feishin)

### 部署安装

如果你想在外网也能访问你的音乐库，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以参考 [这份详细攻略](https://www.passnat.com/posts/p)，一步步教你如何在全平台上快速上手。

#### 最快速的部署方式 - Docker 单命令启动

Feishin 提供了最简便的 Docker 部署方案，只需一条命令即可快速启动：

```bash
docker run --name feishin -p 9180:9180 ghcr.io/jeffvli/feishin:latest
```

执行这条命令后，Docker 会自动拉取最新的 Feishin 镜像并启动容器。稍等片刻，你就可以在浏览器中访问 http://你的IP:9180 来使用 Feishin 了。

**如果遇到端口占用的情况**，可以修改命令中的端口映射。例如，如果想使用 9181 端口，只需改为：

```bash
docker run --name feishin -p 9181:9180 ghcr.io/jeffvli/feishin:latest
```

注意：前面的 9181 是你本地访问的端口，后面的 9180 是容器内的端口，不要改动容器内的端口。

#### 在 NAS 设备上部署

如果你使用飞牛 NAS 或其他 NAS 设备，可以按照以下步骤操作：

添加镜像：打开 NAS 管理界面，找到'本地镜像'或'Docker 镜像'选项

从 URL 添加：选择'从 URL 添加'，输入镜像地址：ghcr.io/jeffvli/feishin:latest

等待下载：镜像下载完成后，点击'启动'按钮

#### Docker Compose 部署方式

如果你需要更灵活的配置，可以使用 Docker Compose。复制下面的配置文件，保存为 docker-compose.yml：

```yaml
version: '3'
services:
  feishin:
    container_name: feishin
    image: 'ghcr.io/jeffvli/feishin:latest'
    environment:
      - SERVER_NAME=navidrome
      - SERVER_LOCK=false
      - SERVER_TYPE=navidrome
      - SERVER_URL=http://你的服务器地址:端口
      - PUID=1000
      - PGID=1000
      - UMASK=002
      - TZ=Asia/Shanghai
    ports:
      - 9180:9180
    restart: unless-stopped
```

然后在配置文件所在目录执行：

```
docker-compose up -d
```

### 重要配置说明

#### 端口映射设置

容器内端口：9180（固定，不要修改）

本地访问端口：可自由设置，默认为 9180

如果你的系统中 9180 端口已被占用，可以修改映射关系。例如改为 8080:9180，这样就能通过 http://IP:8080 访问。

#### 环境变量配置

**必须设置的环境变量：**

变量名: SERVER\_TYPE, 说明: 音乐服务器类型, 示例: navidrome 或 jellyfin

变量名: SERVER\_URL, 说明: 服务器完整地址, 示例: http://192.168.1.100:4533

**可选设置的环境变量：**

变量名: SERVER\_NAME, 说明: 服务器显示名称, 默认值: feishin

变量名: SERVER\_LOCK, 说明: 是否锁定服务器配置, 默认值: false

变量名: TZ, 说明: 时区设置, 默认值: UTC

变量名: PUID, 说明: 用户 ID, 默认值: 1000

变量名: PGID, 说明: 用户组 ID, 默认值: 1000

变量名: PUBLIC\_PATH, 说明: 子路径托管, 默认值: /

**特殊说明：**

如果设置了 SERVER\_LOCK=true，用户将只能修改用户名和密码，无法更改服务器地址

SERVER\_LOCK 仅在同时设置了 SERVER\_NAME、SERVER\_TYPE 和 SERVER\_URL 时才生效

### 首次使用

容器启动成功后，在浏览器中访问 http://你的IP:9180，你会看到 Feishin 的初始化界面。

**第一次使用需要配置音乐服务器：**

点击菜单按钮打开设置

选择'管理服务器'

点击'添加服务器'按钮

填入以下信息：

服务器名称：自定义名称（如'我的音乐库'）

服务器类型：选择 Navidrome 或 Jellyfin

服务器地址：完整的 URL，包括协议和端口（如 http://192.168.1.100:4533）

用户名：你的登录账号

密码：你的登录密码

建议勾选'保存密码'选项，这样下次打开时会自动连接

**Navidrome 用户特别提示：**

为了获得最佳体验，建议在 Navidrome 的配置文件中将 SessionTimeout 设置为较大的值（如 72h），这样可以减少频繁的重新认证。

### 总结

通过以上步骤，你已经成功部署了 Feishin 音乐播放器。现在你可以在任何设备上访问你的个人音乐库，享受高质量的音乐流媒体体验。无论是在家中的 NAS 上还是在云服务器上，Feishin 都能为你提供稳定可靠的音乐播放服务。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/feishin-music-player-deploy-guide-lan-tunneling>


## 相关文章

- [Jellyfin 家庭影院：飞牛 NAS Docker 部署 + 内网穿透，出门手机接着看](jellyfin-nas-media-server-private-cinema.md)
- [Navidrome 飞牛 NAS 部署与公网听歌](navidrome-docker-private-music-server.md)
- [阿里云盘FUSE挂载工具,将云盘变身NAS私人影院,配合内网穿透远程在线观看](aliyun-drive-fuse-mount-nas-private-cinema-remote-viewing.md)
- [Jellyseerr开源影视请求管理系统，NAS一键部署Jellyfin媒体库，内网穿透远程访问审批影视资源](jellyseerr-nas-jellyfin-media-request-approval-system.md)
- [自托管Kyoo影视库服务器，智能识别无需配置，内网穿透远程访问，一次部署永久使用](self-hosted-kyoo-streaming-server-intelligent-recognition-setup-free-internal-network-traversal-remote-access-one-time-deployment-permanent-use.md)
