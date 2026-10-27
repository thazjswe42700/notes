---
title: "家庭智能照片管理系统Photonix，AI识别分类，NAS一键部署，内网穿透远程访问"
date: 2025-10-16
description: "Photonix 是自托管家庭照片管理系统，采用AI智能识别技术，支持对象识别、地理位置感知和色彩分析。在自家服务器部署运行，通过任何设备随时访问照片库，隐私安全且无容量限制。本指南详细介绍Docker Compose快速部署步骤、配置说明、照片导入和升级方法，帮助用户轻松搭建个人照片管理平台。"
canonical: https://www.passnat.com/posts/photonix-ai-photo-management-nas-deployment
---

## 项目介绍

Photonix 是一款功能强大的家庭照片管理系统，基于现代化的网络技术构建而成。它允许你在自己的家庭服务器上部署运行，通过任何设备随时随地访问和管理你的整个照片库。

这个项目最大的亮点在于它采用了先进的人工智能算法，能够自动进行对象识别、地理位置感知和色彩分析等智能操作，让你可以通过智能筛选快速找到想要的照片。无论是按内容搜索、按地点查找还是按色调分类，都能轻松实现。

相比于云端服务，Photonix 让你的所有照片数据都掌握在自己手中，隐私性更强，同时也不受云存储容量和流量的限制。

项目地址： [点我跳转](https://github.com/photonixapp/photonix)

## 部署安装

如果你需要在外出时也能访问家里的照片库，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台快速上手指南。

### 使用 Docker Compose 快速部署

**第一步：准备配置文件**

打开你的 NAS 设备或任何能运行 Docker 的主机，创建一个新的项目目录并下载官方配置文件：

```bash
mkdir photonix
cd photonix
curl https://raw.githubusercontent.com/photonixapp/photonix/master/docker/docker-compose.example.yml > docker-compose.yml
```

**第二步：创建数据目录**

为照片和数据库创建必要的存储文件夹：

```
mkdir -p data/photos data/db data/raw-photos-processed data/cache data/models
```

**第三步：启动服务**

运行以下命令启动 Photonix 及其依赖服务（PostgreSQL 数据库和 Redis 缓存）：

```
docker-compose up -d
```

**第四步：创建管理员账户**

等待容器完全启动后（约 30 秒），在新的终端窗口运行以下命令创建你的管理员账户。请将 YOUR\_USERNAME 替换为你想要的用户名：

```
docker-compose run photonix python photonix/manage.py createsuperuser --username YOUR_USERNAME --email your_email@example.com
```

**第五步：创建照片库**

接着创建你的第一个照片库，同样将 YOUR\_USERNAME 替换为上一步设置的用户名：

```
docker-compose run photonix python photonix/manage.py create_library YOUR_USERNAME 'My Photo Library'
```

**第六步：开始使用**

打开浏览器访问 http://localhost:8888，使用刚才创建的账户登录，你就可以开始管理你的照片了。

### Docker Compose 配置说明

如果你需要自定义配置，可以参考以下配置文件模板：

```yaml
version: '3'

services:
  postgres:
    container_name: photonix-postgres
    image: postgres:11.1-alpine
    environment:
      POSTGRES_DB: photonix
      POSTGRES_PASSWORD: password
    volumes:
      - ./data/db:/var/lib/postgresql/data

  redis:
    container_name: photonix-redis
    image: redis:6.2.2

  photonix:
    container_name: photonix
    image: photonixapp/photonix:latest
    ports:
      - '8888:80'
    environment:
      ENV: prd
      POSTGRES_HOST: postgres
      POSTGRES_DB: photonix
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      REDIS_HOST: redis
      ALLOWED_HOSTS: '*'
    volumes:
      - ./data/photos:/data/photos
      - ./data/raw-photos-processed:/data/raw-photos-processed
      - ./data/cache:/data/cache
      - ./data/models:/data/models
    links:
      - postgres
      - redis
```

### 配置说明

**端口映射：**

容器内部使用 80 端口，映射到主机的 8888 端口。如果 8888 端口已被占用，可修改为其他端口（如 '8889:80'），但容器内的 80 端口保持不变。

**存储目录：**

./data/photos - 你的照片存储位置，将你的照片放在这个文件夹内，系统会自动检测并导入
./data/db - 数据库数据持久化目录
./data/cache - 缩略图和缓存数据存储目录
./data/models - AI 模型数据存储目录

**环境变量设置：**

必须设置：
- POSTGRES\_PASSWORD - 数据库密码（建议修改为强密码）
可选设置：
- ALLOWED\_HOSTS - 允许访问的主机名，默认为 '\*' 表示允许所有主机访问
其他环境变量保持默认即可。

**照片导入**

将你的照片放入 ./data/photos 文件夹，系统会自动检测并导入。系统会自动进行图像分析，提取对象、位置和色彩信息，这个过程可能需要一些时间，请耐心等待。

**升级更新**

如需升级到最新版本，运行以下命令：

```
docker-compose pull
docker-compose up -d
```

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/photonix-ai-photo-management-nas-deployment>


## 相关文章

- [家庭私人照片管理系统PhotoStructure，NAS一键部署，内网穿透远程访问你的回忆](photostructure-nas-private-photo-management-remote-access.md)
- [私人云相册PhotoPrism完整部署指南，AI智能分类照片无限存储隐私保护](private-cloud-photo-album-photoprism-complete-deployment-guide-ai-intelligent-photo-classification-unlimited-storage-privacy-protection.md)
- [Ralbum个人相册管理系统，NAS极简部署，内网穿透外网访问，照片库智能管理](ralbum-personal-photo-album-nas-deployment-intranet-access.md)
- [自托管私人相册HomeGallery，AI智能识别，NAS极简部署，内网穿透远程访问](home-gallery-private-photos-ai-recognition-nas-deployment-remote-access.md)
- [自托管照片管理系统 Immich 部署指南，NAS 快速搭建私人相册库，支持人脸识别和智能备份](immich-nas-self-hosted-photo-management-deployment-guide.md)
