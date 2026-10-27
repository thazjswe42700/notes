---
title: "自托管私有云存储OASIS：Docker极速部署，NAS内网穿透远程访问文件服务器"
date: 2025-10-16
description: "OASIS是一款自托管私有云文件服务器，采用Svelte+Rocket+Tailwind技术栈构建。支持文件上传下载、预览、搜索、播放列表、外链分享等功能，兼容多种文件格式。适合个人文件管理、家庭媒体库和团队文件共享，界面简洁美观，支持中英文双语和用户认证。"
canonical: https://www.passnat.com/posts/oasis-self-hosted-private-cloud-storage-docker-nas-remote-access
---

## 项目介绍

OASIS 是一款功能强大的自托管文件服务器，为用户提供了一个完整的私有云存储解决方案。基于 Svelte + Rocket + Tailwind 的现代技术栈构建，OASIS 支持文件上传下载、预览、搜索、播放列表、外链分享等丰富功能。

无论是个人文件管理、家庭媒体库还是小型团队文件共享，OASIS 都能轻松胜任。项目支持多种文件格式预览，包括文本、图片、音频、视频、PDF 和字幕文件，让你的文件管理体验更加便捷高效。界面简洁美观，支持中英文双语，用户认证机制确保数据安全。

项目地址： [点我跳转](https://github.com/machengim/oasis)

## 部署安装

想要在办公室或外出时也能访问你的私有文件库？推荐使用内网穿透工具来实现。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地安全地访问你的 OASIS 文件服务器。详细的配置教程可以 [点我查看](https://www.passnat.com/posts/p)，包含全平台上手指南。

### 使用 Docker 快速部署

打开你的 NAS 设备或服务器的 Docker 管理面板，按照以下步骤操作：

**第一步：添加 Docker 镜像**

点击'本地镜像' - '添加镜像' - '从 URL 添加'，在镜像地址栏填入：

```
machengim/oasis:latest
```

等待镜像下载完成。

**第二步：启动容器**

点击启动按钮，进入容器配置界面。

**第三步：配置开机自启**

勾选'开机自动启动'，确保 NAS 重启后服务能自动运行。

**第四步：端口映射设置**

在端口设置部分，将容器端口 8000 映射到主机端口。如果你的 8000 端口已被占用，可以修改为其他端口（如 8001、8002 等），但容器内部端口需保持为 8000。

映射格式示例：

主机端口 8000 → 容器端口 8000（默认）

或主机端口 8001 → 容器端口 8000（如有冲突）

**第五步：存储目录配置**

在存储位置设置中，需要创建并映射以下两个目录：

/opt/oasis/data - 应用数据目录（存储配置和数据库文件）

/home/storage - 文件存储目录（你的实际文件存放位置）

建议在 NAS 上预先创建这两个文件夹，然后在 Docker 配置中分别映射。

**第六步：环境变量设置**

OASIS 无需特殊的环境变量配置，保持默认即可。如果你有特殊需求，可以在高级选项中自行添加。

**第七步：完成部署**

所有配置完成后，点击'启动'按钮。容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:8000
```

即可打开 OASIS 文件服务器的管理界面。

### 首次使用

首次访问时，系统会提示你创建管理员账号。设置好用户名和密码后，就可以开始上传和管理你的文件了。支持的功能包括：

📁 文件上传、下载、删除

🔍 全文搜索

🎬 视频播放（支持字幕）

🎵 音乐播放列表

📄 PDF 预览

🔗 生成文件外链分享

🌐 中英文双语支持

本篇教程结束

---

原文：<https://www.passnat.com/posts/oasis-self-hosted-private-cloud-storage-docker-nas-remote-access>


## 相关文章

- [轻量级文件服务器 DUFS 快速部署指南 NAS Docker 一键启用文件共享搜索上传](lightweight-file-server-dufs-quick-deployment-guide-nas-docker-one-click-enable-file-sharing-search-upload.md)
- [自建文件服务器 GoHTTPServer Docker极简部署，NAS内网穿透远程访问文件](self-hosted-file-server-gohttpserver-docker-deployment-nas-intranet-penetration-remote-access.md)
- [无需公网IP搭建私人网盘，AList聚合多云存储，Docker极速部署NAS文件管理系统](alist-docker-nas-private-cloud-storage.md)
- [自建远程文件浏览器Mikochi，极简部署NAS文件管理，支持内网穿透外网访问](mikochi-remote-file-browser-nas-file-management-intranet-penetration.md)
- [私密文件分享平台 ProjectSend Docker 部署指南，NAS 内网穿透安全共享文件](private-file-sharing-projectsend-docker-deployment-nas-intranet-penetration-secure-file-sharing.md)
