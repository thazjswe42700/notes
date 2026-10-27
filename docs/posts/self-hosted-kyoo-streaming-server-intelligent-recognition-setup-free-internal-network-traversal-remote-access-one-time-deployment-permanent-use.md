---
title: "自托管Kyoo影视库服务器，智能识别无需配置，内网穿透远程访问，一次部署永久使用"
date: 2025-10-16
description: "Kyoo是自托管影视媒体服务器，专业替代Jellyfin和Plex。支持电影、电视剧、动画流媒体播放，智能自动识别文件，无需复杂配置。采用微服务架构，集成Meilisearch搜索、动态转码、视频预览等功能。支持Web、Android客户端，计划推出Chromecast和Android-TV。一次配置永久使用，轻松搭建私人影视库。"
canonical: https://www.passnat.com/posts/self-hosted-kyoo-streaming-server-intelligent-recognition-setup-free-internal-network-traversal-remote-access-one-time-deployment-permanent-use
---

## 项目介绍

Kyoo 是一款自托管的影视媒体服务器，专注于电影、电视剧和动画内容的流媒体播放。作为 Jellyfin 和 Plex 的优秀替代品，Kyoo 凭借其出色的智能化设计和低维护特性脱颖而出。

与其他媒体服务器不同，Kyoo 无需复杂的文件夹结构或手动元数据编辑，即使是命名奇特的文件也能被正确识别和匹配。它采用微服务架构，集成了 Meilisearch 搜索引擎、动态转码、视频预览缩略图等强大功能。支持多种客户端（Web、Android），并计划在 2025 年春夏推出 Chromecast 和 Android-TV 支持。

Kyoo 坚持'一次配置，永久使用'的理念，让你可以轻松搭建自己的私人影视库，随时随地享受高质量的流媒体体验。

项目地址：[点我跳转](https://github.com/zoriya/kyoo)

## 部署安装

若要在外网也能访问你的 Kyoo 实例，建议配置内网穿透服务。推荐使用[帕斯内网穿透](https://passnat.com)这类专业的穿透工具，先注册账号备用。具体的配置步骤可以参考[帕斯内网穿透全平台上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

Kyoo 采用 Docker Compose 多容器部署方案，包含后端、前端、数据库、搜索引擎、消息队列和转码服务等多个组件。为了让技术小白也能快速上手，我们推荐使用官方的 Docker Compose 配置文件。

### 第一步：下载配置文件

首先，你需要下载两个关键的配置文件：

1. **docker-compose.yml** - [点击下载](https://github.com/zoriya/kyoo/releases/latest/download/docker-compose.yml)

2. **.env 配置文件** - [点击下载](https://github.com/zoriya/kyoo/releases/latest/download/env.example)

将这两个文件放在同一个文件夹中。注意：**.env 文件必须命名为 `.env`**（文件名前面有一个点），这样 Docker Compose 才能正确读取配置。

### 第二步：配置 .env 文件

下载的 `.env` 文件包含了所有可配置选项。打开它并根据你的需求进行配置：

**必须配置的选项：**

- `LIBRARY\_ROOT` - 你的视频库路径（默认：`./video`）

- `CACHE\_ROOT` - 转码缓存目录（默认：`/tmp/kyoo\_cache`）

- `POSTGRES\_USER` - 数据库用户名（默认：`kyoo`）

- `POSTGRES\_PASSWORD` - 数据库密码（建议修改为强密码）

- `POSTGRES\_DB` - 数据库名称（默认：`kyoo`）

- `RABBITMQ\_DEFAULT\_USER` - 消息队列用户名

- `RABBITMQ\_DEFAULT\_PASS` - 消息队列密码

**可选配置：**

- `COMPOSE\_PROFILES` - 硬件加速类型（`cpu`、`vaapi`、`qsv`、`nvidia`，默认：`cpu`）

- `GOCODER\_PRESET` - 转码质量预设（`fast`、`medium`、`slow`，默认：`fast`）

- `THEMOVIEDB\_APIKEY` - 电影数据库 API 密钥（可选，使用官方默认密钥）

- `TVDB\_APIKEY` - 电视剧数据库 API 密钥（可选）

- `PUBLIC\_URL` - 你的公网访问地址（用于 OIDC 认证，默认：`http://localhost:8901`）

**OIDC 认证配置（可选）：**

如果你想使用 Google、Discord 等第三方登录，可以配置相应的 OIDC 选项：

```
OIDC_DISCORD_CLIENTID=你的客户端ID
OIDC_DISCORD_SECRET=你的客户端密钥
```

### 第三步：准备视频库目录

在 docker-compose.yml 所在目录创建一个 `video` 文件夹，用来存放你的电影和电视剧文件：

```
mkdir video
```

如果你的视频文件存放在其他位置，只需修改 `.env` 中的 `LIBRARY\_ROOT` 参数指向正确的路径即可。

### 第四步：启动 Kyoo

打开终端，进入 docker-compose.yml 和 .env 文件所在的目录，执行以下命令启动 Kyoo：

```bash
docker compose up -d
```

Docker 会自动下载所有必要的镜像并启动各个服务。首次启动可能需要几分钟，请耐心等待。

### 第五步：访问 Kyoo

启动完成后，打开浏览器访问：

```
http://localhost:8901
```

如果一切正常，你将看到 Kyoo 的精美界面。首次访问时，系统会提示你创建管理员账户。

## 硬件加速配置（可选）

如果你的服务器配备了 GPU，可以启用硬件加速来提升转码性能：

**Intel/AMD GPU（VA-API）：**

修改 `.env` 中的 `COMPOSE\_PROFILES=vaapi`，然后重启服务。

**NVIDIA GPU：**

1. 先安装[nvidia-container-toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)

2. 修改 `.env` 中的 `COMPOSE\_PROFILES=nvidia`

3. 重启服务

## 常见问题

**Q：如何添加我的视频文件？**

A：将视频文件放在 `LIBRARY\_ROOT` 指定的目录中。Kyoo 会自动扫描并识别电影和电视剧，无需手动创建文件夹结构。

**Q：如何排除某些目录不被扫描？**

A：在要排除的目录中创建一个名为 `.ignore` 的空文件即可。Kyoo 会递归忽略该目录及其所有子目录。

**Q：如何更新 Kyoo？**

A：在配置文件目录执行 `docker compose pull` 更新镜像，然后运行 `docker compose up -d` 重启服务。

**Q：如何卸载 Kyoo？**

A：执行 `docker compose down` 停止所有服务，然后删除配置文件即可。

## 总结

通过以上步骤，你已经成功部署了一个功能完整的 Kyoo 媒体服务器。它将自动扫描你的视频库，提供强大的搜索功能、动态转码、多设备支持等特性。无论是家庭影院还是小型工作室，Kyoo 都能提供专业级的流媒体体验。

享受你的私人影视库吧！🎬🎉

---

原文：<https://www.passnat.com/posts/self-hosted-kyoo-streaming-server-intelligent-recognition-setup-free-internal-network-traversal-remote-access-one-time-deployment-permanent-use>


## 相关文章

- [Jellyseerr开源影视请求管理系统，NAS一键部署Jellyfin媒体库，内网穿透远程访问审批影视资源](jellyseerr-nas-jellyfin-media-request-approval-system.md)
- [免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库](lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access.md)
- [自托管生活追踪平台Ryot完整部署指南，NAS一键安装，内网穿透远程访问](self-hosted-life-tracking-ryot-deployment-guide-nas-one-click-install-internal-network-penetration-remote-access.md)
- [私有部署开源聊天应用VoceChat，NAS轻松搭建安全私密聊天系统，内网穿透远程访问](vocechat-private-deployment-nas-internal-network-penetration.md)
- [MeiliSearch闪电搜索引擎Docker极速部署，内网穿透远程访问一键搭建](meilisearch-docker-deployment-intranet-penetration-remote-access.md)
