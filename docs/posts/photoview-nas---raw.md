---
title: "自建相册管理系统Photoview，NAS极简部署内网穿透远程访问，摄影师RAW格式完美支持"
date: 2025-10-16
description: "专业自建相册系统，基于Go和React打造。支持RAW格式、EXIF数据、人脸识别和视频处理。快速生成缩略图，轻松管理数千张高分辨率照片。完美适配摄影爱好者需求，提供多用户支持和灵活分享选项。安全可靠的私人相册解决方案。"
canonical: https://www.passnat.com/posts/photoview-nas---raw
---

## 项目介绍

Photoview 是一款专为摄影爱好者打造的自建相册管理系统，基于 Go 和 React 构建。它提供了一个简洁高效的照片浏览体验，能够快速处理数千张高分辨率照片。

系统会自动扫描你指定目录下的所有照片和视频，并生成缩略图加速浏览。照片在网页上的组织方式与你的文件系统完全一致，让你可以轻松管理和分享珍贵的回忆。特别是对于摄影师常用的 RAW 格式和 EXIF 数据，Photoview 都提供了完美的支持。

项目地址：[点我跳转](https://github.com/photoview/photoview)

## 部署安装

如果你需要在外网访问家里的相册库，可以配置内网穿透服务来实现。推荐使用[帕斯内网穿透](https://passnat.com)这样的工具，先注册账号备好。具体的详细操作步骤可以参考[这份完整攻略](https://www.passnat.com/posts/p)，包含了全平台上手的详细说明。

### 快速部署指南

**准备工作**

确保你的 NAS 或服务器上已经安装了 Docker 和 Docker Compose。

**第一步：下载配置文件**

从项目的 docker-compose 示例文件夹中下载所有配置文件到你想要存放 Photoview 数据的目录。

**第二步：配置文件修改**

复制下列的配置文件到你的部署目录，命名为 'docker-compose.yml'：

```yaml
services:
  photoview-prepare:
    image: photoview/photoview:2
    hostname: photoview-prepare
    container_name: photoview-prepare
    user: root
    entrypoint: []
    command: /bin/bash -c "sleep 1 && chown -R photoview:photoview /home/photoview/media-cache"
    cap_add:
      - CHOWN
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
      - "/etc/timezone:/etc/timezone:ro"
      - "${HOST_PHOTOVIEW_LOCATION}/storage:/home/photoview/media-cache"

  photoview:
    image: photoview/photoview:2
    hostname: photoview
    container_name: photoview
    restart: unless-stopped
    ports:
      - "8000:80"
    depends_on:
      photoview-prepare:
        condition: service_completed_successfully
      mariadb:
        condition: service_healthy
    environment:
      PHOTOVIEW_DATABASE_DRIVER: mysql
      PHOTOVIEW_MYSQL_URL: "${MARIADB_USER}:${MARIADB_PASSWORD}@tcp(photoview-mariadb)/${MARIADB_DATABASE}"
      PHOTOVIEW_LISTEN_IP: "0.0.0.0"
      MAPBOX_TOKEN: ${MAPBOX_TOKEN}
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
      - "/etc/timezone:/etc/timezone:ro"
      - "${HOST_PHOTOVIEW_LOCATION}/storage:/home/photoview/media-cache"
      - "${HOST_PHOTOVIEW_MEDIA_ROOT}:/photos:ro"

  mariadb:
    image: mariadb:lts
    hostname: photoview-mariadb
    container_name: photoview-mariadb
    restart: unless-stopped
    environment:
      MARIADB_AUTO_UPGRADE: "1"
      MARIADB_DATABASE: ${MARIADB_DATABASE}
      MARIADB_USER: ${MARIADB_USER}
      MARIADB_PASSWORD: ${MARIADB_PASSWORD}
      MARIADB_ROOT_PASSWORD: ${MARIADB_ROOT_PASSWORD}
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
      - "/etc/timezone:/etc/timezone:ro"
      - "${HOST_PHOTOVIEW_LOCATION}/database/mariadb:/var/lib/mysql"
    healthcheck:
      test: healthcheck.sh --connect --innodb_initialized
      interval: 1m
      timeout: 5s
      retries: 5
      start_period: 3m
```

同时创建 '.env' 文件，配置必要的环境变量：

```
# 数据存储位置（修改为你的实际路径）
HOST_PHOTOVIEW_LOCATION=/path/to/photoview/data

# 照片存储位置（修改为你的照片所在目录）
HOST_PHOTOVIEW_MEDIA_ROOT=/path/to/your/photos

# 数据库配置
MARIADB_DATABASE=photoview
MARIADB_USER=photoview
MARIADB_PASSWORD=your_secure_password_here
MARIADB_ROOT_PASSWORD=your_root_password_here

# 可选：Mapbox 地图功能（需要在 https://account.mapbox.com 申请免费 token）
MAPBOX_TOKEN=
```

**第三步：启动服务**

打开飞牛 NAS 或其他 NAS 设备的终端，进入到你放置 docker-compose.yml 的目录，执行以下命令启动 Photoview：

```
docker-compose up -d
```

系统会自动下载所需的镜像并启动所有服务。首次启动可能需要几分钟时间。

**第四步：初始化配置**

启动完成后，在浏览器中访问 'http://你的NAS地址:8000'

第一次访问时会看到初始化向导：

1. 输入管理员用户名和密码

2. 在'照片路径'字段中填入 '/photos'（这是容器内的路径，对应你在 docker-compose.yml 中挂载的实际照片目录）

3. 点击完成创建账户

**第五步：扫描照片**

新建的账户会自动获得访问权限。首次使用需要手动扫描照片：

- 登录后进入 Settings（设置）

- 点击 'Scan All' 按钮开始扫描

- 系统会自动识别照片、生成缩略图并解析 EXIF 信息

### 环境变量说明

**必须设置**

- 'HOST\_PHOTOVIEW\_LOCATION'：Photoview 数据和缓存的存储位置

- 'HOST\_PHOTOVIEW\_MEDIA\_ROOT'：你的照片存储位置

- 'MARIADB\_PASSWORD'：数据库密码（建议使用复杂密码）

- 'MARIADB\_ROOT\_PASSWORD'：数据库 root 密码

**可选设置**

- 'MAPBOX\_TOKEN'：用于启用地图功能的 Mapbox API 密钥（可选，不填也能正常使用）

- 'PHOTOVIEW\_VIDEO\_HARDWARE\_ACCELERATION'：视频硬件加速（支持 qsv、vaapi、nvenc）

### 端口说明

- **8000**：Photoview 网页界面访问端口。如果 8000 端口被占用，可以修改 docker-compose.yml 中的 'ports' 字段，例如改为 '8001:80' 即可使用 8001 端口访问。容器内部始终运行在 80 端口，修改前面的数字不会影响应用功能。

### 数据持久化

系统会在以下位置保存数据，确保这些目录有足够的存储空间：

- '${HOST\_PHOTOVIEW\_LOCATION}/storage'：照片缓存和缩略图

- '${HOST\_PHOTOVIEW\_LOCATION}/database/mariadb'：数据库文件

### 常见问题

**密码包含特殊字符**

如果数据库密码中包含 '@' 等特殊字符，需要进行 URL 编码。例如 'p@ss' 应该改为 'p%40ss'。

**照片权限问题**

确保 Photoview 容器有权限读取你的照片文件夹。如果遇到权限错误，可以运行：

```
chmod -R 755 /path/to/your/photos
```

**首次扫描很慢**

首次扫描需要生成所有照片的缩略图并提取 EXIF 信息，数量越多耗时越长。这是正常的，耐心等待即可。后续增加的新照片扫描速度会更快。

## 项目特色

✨ **摄影师友好**：完美支持 RAW 格式和 EXIF 数据，保留照片的所有信息

🎬 **视频支持**：自动优化常见视频格式用于网页播放

👥 **人脸识别**：自动检测照片中的人脸，将同一个人的照片聚合在一起

🔒 **安全可靠**：所有媒体资源都有 Cookie 令牌保护，密码采用安全哈希存储

⚡ **性能优异**：自动生成缩略图，大图片按需加载，浏览体验流畅

🌍 **多用户支持**：支持多用户管理，每个用户可以拥有独立的照片库

🔗 **灵活分享**：支持公开链接分享相册或单张照片，可选密码保护

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/photoview-nas---raw>
