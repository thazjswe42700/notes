---
title: "私人云相册PhotoPrism完整部署指南，AI智能分类照片无限存储隐私保护"
date: 2025-10-16
description: "PhotoPrism是一款隐私友好的AI驱动照片应用，支持自托管部署。提供人脸识别、地点标记、内容标签等功能，完全掌控您的数据。支持RAW格式、视频、Live Photos，跨平台PWA应用，内置世界地图和WebDAV同步。通过Docker Compose快速部署，适合家庭和小型团队使用。"
canonical: https://www.passnat.com/posts/private-cloud-photo-album-photoprism-complete-deployment-guide-ai-intelligent-photo-classification-unlimited-storage-privacy-protection
---

## PhotoPrism - 打造私人云相册的最佳选择

### 项目介绍

PhotoPrism 是一款功能强大的AI驱动照片应用，致力于为用户提供最隐私友好的相册管理解决方案。它基于现代化技术栈构建，支持跨平台部署，可以在家庭、私有服务器或云端运行，让您拥有完全的数据掌控权。

这个项目的核心理念是保护您的隐私——您的数据永远不会被共享给Google、Amazon、Microsoft或Apple，除非您主动上传。PhotoPrism采用AI技术自动标记和分类照片，支持人脸识别、地点识别、内容标签等功能，让您轻松整理和查找数百万张照片和视频。

无论您使用手机、平板还是桌面电脑，PhotoPrism提供的PWA应用都能提供原生应用般的体验。它支持RAW格式、各类视频格式、Live Photos，内置高分辨率世界地图，支持WebDAV同步，让您随时随地畅享自己的回忆。

**项目地址：**[**PhotoPrism GitHub**](https://github.com/photoprism/photoprism)

### 部署安装

如果您想在外网也能访问家中的PhotoPrism，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p) 进行配置，这样即使在办公室或出差途中也能随时查看您的私人相册。

#### 最快速上手 - Docker Compose 方式

PhotoPrism 官方推荐使用 Docker Compose 进行部署，这是最简单快速的方式。

##### 第一步：下载配置文件

在您选择的文件夹中，执行以下命令下载官方配置文件：

```
wget https://dl.photoprism.app/docker/compose.yaml
```

##### 第二步：修改配置

编辑下载好的 `compose.yaml` 文件，最重要的是修改管理员密码。找到以下行并修改：

```yaml
services:
  photoprism:
    environment:
      PHOTOPRISM_ADMIN_PASSWORD: 'your-secure-password-here'  # 改成你自己的密码，最少8个字符
      PHOTOPRISM_ADMIN_USER: 'admin'
      PHOTOPRISM_SITE_URL: 'http://localhost:2342/'
      PHOTOPRISM_ORIGINALS_LIMIT: 5000
      PHOTOPRISM_WORKERS: 2
      PHOTOPRISM_DISABLE_TENSORFLOW: 'false'
```

**关键配置说明：**

PHOTOPRISM\_ADMIN\_PASSWORD：必须设置，这是您的管理员登录密码，务必设置为安全的密码
PHOTOPRISM\_WORKERS：处理线程数，根据您服务器的CPU核心数调整，建议设置为CPU核心数的一半
PHOTOPRISM\_DISABLE\_TENSORFLOW：是否禁用AI功能，默认启用，如果服务器性能较弱可以设置为'true'禁用
PHOTOPRISM\_READONLY：只读模式，如果设置为'true'则禁用上传、删除等写操作
PHOTOPRISM\_SITE\_URL：网站访问地址，如果配置了内网穿透则改成穿透后的地址

**存储卷配置：**

确保您的 `compose.yaml` 中包含以下卷挂载配置：

```yaml
volumes:
  - '~/Pictures:/photoprism/originals'        # 您的照片文件夹
  - './storage:/photoprism/storage'           # 缓存和配置文件夹
  - 'photoprism-db:/var/lib/mysql'           # 数据库存储
```

如果您的照片存放在其他位置，比如NAS的 `/mnt/photos`，修改为：

```yaml
volumes:
  - '/mnt/photos:/photoprism/originals'
  - './storage:/photoprism/storage'
  - 'photoprism-db:/var/lib/mysql'
```

##### 第三步：启动服务

在保存配置文件的目录中，执行以下命令启动PhotoPrism：

```bash
docker compose up -d
```

稍等片刻，服务会在后台启动。您可以通过以下命令查看启动日志：

```bash
docker compose logs -f --tail=100
```

##### 第四步：访问应用

打开浏览器，访问 `http://localhost:2342/`（如果部署在NAS或其他服务器，将localhost替换为对应的IP地址）

您会看到登录界面，使用用户名 `admin` 和您设置的密码登录即可。

##### 第五步：索引您的照片库

首次登录后，PhotoPrism会自动开始索引您在 `/photoprism/originals` 文件夹中的照片。这个过程可能需要一些时间，具体取决于您的照片数量和服务器性能。

在设置中，您可以：
启用人脸识别来自动识别家人和朋友
配置地点识别来标记照片的拍摄地点
启用自动标签来根据内容分类照片

#### 端口和性能配置

如果默认的 2342 端口被占用，您可以在 `compose.yaml` 中修改：

```
ports:
  - '8080:2342'  # 改成 8080:2342，然后访问 http://localhost:8080
```

**性能建议：**

最小配置：4GB内存，建议配置4GB交换空间
处理大量RAW格式和高分辨率全景图时需要更多内存
如果服务器内存不足，可以减少 `PHOTOPRISM\_WORKERS` 的值
建议将存储文件夹放在SSD上以获得最佳性能

#### 常用命令

```bash
# 查看日志
docker compose logs -f

# 停止服务
docker compose stop

# 重启服务
docker compose restart

# 更新镜像
docker compose pull
docker compose up -d

# 修改管理员密码
docker compose exec photoprism photoprism passwd admin

# 完整重新索引
docker compose exec photoprism photoprism index -f

# 查看配置
docker compose exec photoprism photoprism show config
```

### 项目总结

PhotoPrism 是一个完全自托管的、隐私至上的照片管理解决方案。它不仅提供了强大的AI功能来帮助您组织照片，还确保您的所有数据始终掌握在自己手中。无论是家庭用户还是小型团队，都能通过简单的Docker部署快速上手。

如果您重视隐私、希望拥有自己的云相册、或者想要一个功能完整的照片管理系统，PhotoPrism 绝对值得一试。

本篇教程到此结束，祝您使用愉快！

---

原文：<https://www.passnat.com/posts/private-cloud-photo-album-photoprism-complete-deployment-guide-ai-intelligent-photo-classification-unlimited-storage-privacy-protection>
