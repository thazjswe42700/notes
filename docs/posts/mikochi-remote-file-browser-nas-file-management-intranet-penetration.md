---
title: "自建远程文件浏览器Mikochi，极简部署NAS文件管理，支持内网穿透外网访问"
date: 2025-10-16
description: "Mikochi是极简设计的远程文件浏览器，专为自建服务器和NAS打造。采用JavaScript/Preact前端和Go/Gin后端架构，提供文件浏览、搜索、上传下载、重命名删除、目录打包等功能。开箱即用，一条命令即可启动。支持模糊搜索、VLC/MPV播放器流传输，界面简洁易用，技术小白也能快速上手。"
canonical: https://www.passnat.com/posts/mikochi-remote-file-browser-nas-file-management-intranet-penetration
---

## 项目介绍

Mikochi 是一个极简设计的远程文件浏览器，专为自建服务器和 NAS 而生。它采用 JavaScript/Preact 前端和 Go/Gin 后端架构打造，提供了一个简洁而强大的文件管理解决方案。无论你身在何处，都可以轻松访问和管理服务器上的文件。

这个项目最大的特点就是'开箱即用'——无需复杂配置，一条命令就能启动。它支持文件浏览、模糊搜索、上传下载、重命名删除、目录打包下载，甚至还能直接将文件流传输到 VLC/MPV 播放器。界面简洁易用，即使是技术小白也能快速上手。

项目地址：[点我跳转](https://github.com/zer0tonin/Mikochi)

## 部署安装

如果你需要在外网也能访问这个项目，建议配置内网穿透功能。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以参考 [全平台上手攻略](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### Docker 单命令启动

打开你的 NAS 设备或服务器，在本地镜像管理中选择'添加镜像'，从 URL 添加。

镜像地址填写：zer0tonin/mikochi:latest

等待镜像下载完成后，点击启动按钮。在启动容器前，需要进行以下配置：

端口映射设置：

容器端口：8080（容器内部监听端口，保持不变）

主机端口：8080（可根据需要修改，如果端口被占用可改为其他端口如 8081）

存储位置配置：

需要创建一个文件夹用于存储数据。在 NAS 中创建一个名为 mikochi\_data 的文件夹，然后将其映射到容器内的 /data 目录。这样你上传的文件和配置都会被保存下来。

环境变量设置：

必须设置：

DATA\_DIR：/data（数据存储目录）

USERNAME：你的登录用户名（建议改为自己的用户名）

PASSWORD：你的登录密码（务必设置强密码）

可选设置：

HOST：0.0.0.0:8080（监听地址和端口，一般不需要修改）

GZIP：false（是否启用 gzip 压缩，可选）

NO\_AUTH：false（是否禁用身份验证，不建议改为 true）

其他变量保持默认即可，直接下一步启动容器。

启动成功后，在浏览器中访问 http://你的NAS地址:8080，使用刚才设置的用户名和密码登录，就可以开始使用 Mikochi 了。

### 功能体验

登录后你会看到一个简洁的文件浏览界面。你可以：

🔍 通过模糊搜索快速定位文件

📤 上传文件或创建新文件夹

⬇️ 下载单个文件或整个目录（自动打包为 tar.gz）

🎬 直接将视频流发送到 VLC/MPV 播放器

✏️ 重命名或删除文件

这一切都通过一个美观简洁的 Web 界面完成，无需命令行操作。

本篇教程结束

---

原文：<https://www.passnat.com/posts/mikochi-remote-file-browser-nas-file-management-intranet-penetration>


## 相关文章

- [Win11React网页版Windows11桌面一键Docker部署，浏览器在线体验完整系统界面](win11-react-docker-browser-desktop.md)
- [无需公网IP搭建私人网盘，AList聚合多云存储，Docker极速部署NAS文件管理系统](alist-docker-nas-private-cloud-storage.md)
- [多存储聚合管理工具AList，支持30+云盘本地存储，Docker极简部署NAS内网穿透在线预览](alist-multi-cloud-storage-aggregation-tool-30-plus-cloud-drives-local-storage-docker-simple-deployment-nas-intranet-penetration-online-preview.md)
- [Exatorrent 自托管种子客户端，Go 开发轻量级 Docker 部署，NAS 内网穿透远程下载](exatorrent-self-hosted-torrent-client-go-lightweight-docker-nas-remote-download.md)
- [Hugo极速静态网站生成器NAS部署指南,Go语言编写秒速渲染,内网穿透远程访问](hugo-nas-deployment-guide-go-fast-rendering-remote-access.md)
