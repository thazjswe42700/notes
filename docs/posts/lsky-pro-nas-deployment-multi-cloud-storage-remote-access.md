---
title: "自建云相册Lsky Pro，NAS极简部署，支持多云存储，内网穿透远程访问图片管理分享系统"
date: 2025-10-16
description: "Lsky Pro 是功能强大的自建云相册系统，基于 Laravel 和 Tailwind CSS 构建。支持多种云存储服务、多图上传、瀑布流展示、灵活权限配置和图片水印等功能。本文详细介绍 Docker 部署步骤、容器配置方法、环境变量设置及访问方式，帮助您快速搭建私密高效的个人相册库。"
canonical: https://www.passnat.com/posts/lsky-pro-nas-deployment-multi-cloud-storage-remote-access
---

## 项目介绍

Lsky Pro 是一个功能强大的自建云相册系统，基于 Laravel 和 Tailwind CSS 构建。它支持多种云存储服务（包括本地存储、AWS S3、阿里云 OSS、腾讯云 COS、七牛云、又拍云、SFTP、FTP、WebDAV、Minio 等），提供了开箱即用的图片管理和分享解决方案。

无论是个人相册还是团队协作，Lsky Pro 都能满足你的需求。支持多图上传、拖拽上传、粘贴上传等多种上传方式，强大的瀑布流展示和管理功能，灵活的角色权限配置，以及图片水印、审核等高级功能，让你完全掌控自己的图片资产。

项目地址： [点我跳转](https://github.com/wisp-x/lsky-pro)

## 部署安装

如果你需要在外网也能访问你的相册库，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体操作步骤可参考 [全平台快速上手指南](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### Docker 部署（推荐）

打开飞牛 NAS 或其他 NAS 设备的控制面板，找到'本地镜像'选项。

点击'添加镜像'，选择'从 URL 添加'方式。

在镜像地址栏中填入以下镜像地址：

```
lskyorg/lsky-pro:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

### 容器配置详解

**基础设置**

勾选'开机自动启动'确保服务持久运行，然后进入下一步。

**端口映射**

容器内部运行在 8000 端口，你可以根据需要将其映射到主机的任意端口（如 8080、8888 等）。如果遇到端口被占用的情况，只需修改主机端口号即可，容器内部端口保持 8000 不变。

**存储配置**

Lsky Pro 需要持久化存储来保存上传的图片和配置文件。建议在 NAS 中创建一个专用文件夹（如 '/mnt/lsky-pro'），然后将其映射到容器内的 '/app/storage' 目录。这样即使容器重启，你的数据也不会丢失。

**环境变量设置**

以下是 Lsky Pro 的主要环境变量配置：

必须设置：

APP\_KEY: 应用密钥（用于加密，可使用随机字符串）

DB\_CONNECTION: 数据库类型（支持 mysql、pgsql、sqlite、sqlsrv）

DB\_HOST: 数据库主机地址

DB\_PORT: 数据库端口

DB\_DATABASE: 数据库名称

DB\_USERNAME: 数据库用户名

DB\_PASSWORD: 数据库密码

可选设置：

APP\_DEBUG: 调试模式（true/false，默认 false）

APP\_URL: 应用访问 URL

CACHE\_DRIVER: 缓存驱动（file、redis、memcached 等，默认 file）

SESSION\_DRIVER: 会话驱动（file、cookie 等，默认 file）

如果你使用 SQLite 数据库（最简单的方案），可以不配置上述数据库相关变量，系统会自动使用本地 SQLite。

其他环境变量保持默认即可，无需特殊配置。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:映射的端口号
```

例如，如果你将容器端口 8000 映射到主机的 8080 端口，NAS IP 为 192.168.1.100，则访问地址为：

```
http://192.168.1.100:8080
```

首次访问会进入初始化向导，按照提示完成基础配置即可开始使用。

### 部署完成

到此为止，Lsky Pro 已经成功部署并运行。你现在拥有了一个完全属于自己的私密相册系统，可以安心地管理和分享你的珍贵照片。

本篇教程结束

---

原文：<https://www.passnat.com/posts/lsky-pro-nas-deployment-multi-cloud-storage-remote-access>
