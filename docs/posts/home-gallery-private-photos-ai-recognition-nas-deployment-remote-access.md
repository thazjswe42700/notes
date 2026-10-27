---
title: "自托管私人相册HomeGallery，AI智能识别，NAS极简部署，内网穿透远程访问"
date: 2025-10-16
description: "自托管开源网络相册，集成AI图像识别、人脸检测、相似图搜索等功能。支持标签管理、地理位置查询、PWA移动应用，所有照片视频本地存储，完全保护隐私。界面简洁易用，支持超大规模媒体库。提供Docker快速部署方案，轻松实现远程访问。"
canonical: https://www.passnat.com/posts/home-gallery-private-photos-ai-recognition-nas-deployment-remote-access
---

## 项目介绍

HomeGallery 是一款自托管的开源网络相册应用，专为私人照片和视频浏览而设计。它集成了AI驱动的图像识别、人脸检测、相似图搜索等高级功能，同时提供标签管理、地理位置反向查询、PWA移动应用支持等丰富特性。

这个项目特别适合那些重视隐私、希望完全掌控自己媒体数据的用户。所有照片和视频都存储在你的本地NAS或服务器上，无需依赖任何云服务。界面简洁易用，在手机上也能获得流畅的浏览体验。支持超大规模媒体库，用户已成功管理超过40万张照片。

项目地址：[点我跳转](https://github.com/xemle/home-gallery)

## 部署安装

若要在家外也能访问你的相册库，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先行注册账号以备后用。详细的配置步骤可参考 [全平台快速上手指南](https://www.passnat.com/posts/p)，能够帮你轻松实现远程访问。

### 使用 Docker Compose 快速部署

这是最推荐的部署方式，包含了完整的AI识别服务。

复制以下配置文件到你的NAS或服务器，创建一个 `docker-compose.yml` 文件：

```yaml
version: '3.9'
services:
  api:
    image: xemle/home-gallery-api-server
    environment:
      - BACKEND=wasm
  gallery:
    image: xemle/home-gallery
    environment:
      - GALLERY_API_SERVER=http://api:3000
      - GALLERY_API_SERVER_CONCURRENT=1
      - GALLERY_API_SERVER_TIMEOUT=60
      - GALLERY_OPEN_BROWSER=false
      - GALLERY_WATCH_POLL_INTERVAL=300
    volumes:
      - ./data:/data
      - ${HOME}/Pictures:/data/Pictures
    ports:
      - '3000:3000'
    user: '${CURRENT_USER}'
    entrypoint: ['node', '/app/gallery.js']
    command: ['run', 'server']
```

打开你的飞牛NAS、群晖NAS或其他NAS设备，进入Docker管理界面。

**第一步：导入镜像**

点击「本地镜像」-「添加镜像」-「从URL添加」，分别添加以下两个镜像：
- `xemle/home-gallery-api-server`（AI识别服务）
- `xemle/home-gallery`（主应用）
等待两个镜像下载完成。

**第二步：配置并启动容器**

使用上面的docker-compose配置文件启动服务。如果你的NAS不支持docker-compose，可以分别启动两个容器。

**第三步：端口配置**

系统会自动使用 `3000` 端口。如果该端口已被占用，你可以修改配置中的 `3000:3000` 为其他端口，例如 `8080:3000`（前面的数字是外部访问端口，后面的是容器内端口，容器内端口必须保持为3000）。

**第四步：存储位置设置**

创建一个本地文件夹用于存储相册数据和缓存文件。将 `./data` 目录映射到你NAS上的某个位置，例如 `/mnt/data/home-gallery`。
同时，将你的照片和视频目录映射进去。如果你的照片存放在 `/mnt/photos` 目录下，修改配置中的 `${HOME}/Pictures:/data/Pictures` 为 `/mnt/photos:/data/Pictures`。

**第五步：环境变量说明**

必须配置的环境变量：
- `GALLERY\_API\_SERVER`: AI服务地址，默认为 `http://api:3000`（使用docker-compose时无需修改）
可选配置，根据设备性能调整：
- `GALLERY\_API\_SERVER\_CONCURRENT`: 并发请求数。树莓派等低功耗设备建议设为 `1`，普通NAS可设为 `5`
- `GALLERY\_API\_SERVER\_TIMEOUT`: 超时时间（秒）。树莓派建议 `60`，普通设备可设为 `30`
- `GALLERY\_WATCH\_POLL\_INTERVAL`: 文件监控轮询间隔（毫秒），默认 `300` 适合网络挂载
- `BACKEND`: AI后端类型，可选 `cpu`（最兼容）、`wasm`（推荐）、`node`（最快但仅x86-64）
其他变量保持默认即可，无需手动修改。

**第六步：启动服务**

点击启动按钮，勾选「开机自动启动」。
等待容器启动完成后，在浏览器中访问 `http://你的NAS地址:3000` 即可打开HomeGallery相册。
首次启动时，系统会自动扫描和处理你的照片库，生成缩略图并进行AI分析。这个过程可能需要一些时间，取决于你的媒体库大小。

### 使用 Docker 单命令部署（简化版）

如果你只想快速体验而不需要AI识别功能，可以使用以下单命令：

```bash
docker run -d \
  -v /path/to/your/photos:/data/Pictures \
  -v home-gallery-data:/data \
  -p 3000:3000 \
  xemle/home-gallery
```

将 `/path/to/your/photos` 替换为你实际的照片目录路径。

本篇教程到此结束。现在你已经拥有了一个功能完整的私人相册库，所有数据都掌握在自己手中！

---

原文：<https://www.passnat.com/posts/home-gallery-private-photos-ai-recognition-nas-deployment-remote-access>


## 相关文章

- [家常菜谱聚合平台来做菜，材料有限也能做美食，支持离线PWA，NAS一键Docker部署](home-cooking-recipes-pwa-nas-docker-deployment.md)
- [Cogito个人知识库系统自建指南，NAS极简部署智能笔记管理工具，支持内网穿透远程访问](1-seo-slug-aa-bb-cc2-cogitoseoseo-slug------slug.md)
- [自托管笔记书签管理Benotes Docker部署指南，NAS轻松搭建私有知识库](self-hosted-notes-bookmarks-benotes-docker-nas-private-knowledge-base.md)
- [自建密码生成器Docker一键部署，支持离线模式安全检测泄露，NAS极简配置内网穿透远程访问](self-hosted-password-generator-docker-one-click-deploy-offline-mode-security-breach-detection-nas-simple-config-intranet-penetration-remote-access.md)
- [SideQuests 任务管理系统 Docker 一键部署，NAS 极简配置内网穿透远程访问任务](sidequests-docker-nas-deployment-intranet-penetration.md)
