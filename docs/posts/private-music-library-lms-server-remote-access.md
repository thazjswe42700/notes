---
title: "自建私人音乐库随处畅享，轻量级LMS服务器无公网也能远程访问音乐"
date: 2025-10-16
description: "自托管轻量级音乐流媒体服务器LMS，支持Subsonic API、音乐发现、推荐引擎、播客功能、歌词显示。完全由你掌控，无需第三方服务。支持MusicBrainz识别、ListenBrainz同步、音频转码等高级功能。Docker快速部署，NAS原生支持。"
canonical: https://www.passnat.com/posts/private-music-library-lms-server-remote-access
---

## 项目介绍

LMS（Lightweight Music Server）是一个自托管的轻量级音乐流媒体服务器，让你可以随时随地通过网页界面访问自己的音乐库。这是一个开箱即用的解决方案，完全由你掌控，无需依赖任何第三方音乐流媒体服务。

LMS 支持 Subsonic/OpenSubsonic API，拥有强大的音乐发现功能、推荐引擎、多库支持、播客功能、歌词显示等丰富特性。它采用标签化组织方式，支持 MusicBrainz 识别符、ListenBrainz 同步、音频转码等高级功能。无论你的音乐库有多大，LMS 都能帮你高效地管理和播放。

项目地址： [点我跳转](https://github.com/epoupon/lms)

## 部署安装

如果你想在外网也能访问自己的音乐库，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，通过内网穿透的方式让你在任何地方都能安全地访问家里的音乐服务器。详细的配置步骤可以查看 [这份完整攻略](https://www.passnat.com/posts/p)。

### 使用 Docker 快速部署

打开你的 NAS 设备或任何支持 Docker 的平台，执行以下命令即可快速启动 LMS：

```bash
docker run -d \
  --name lms \
  -p 5082:5082 \
  -v /path/to/your/music:/music \
  -v lms-data:/var/lms \
  epoupon/lms:latest
```

**参数说明：**

`-p 5082:5082` ：将容器内的 5082 端口映射到主机，这是 LMS 的默认访问端口。如果你的主机 5082 端口已被占用，可以改为 `-p 8080:5082` 这样的形式，然后通过 `http://ip:8080` 访问

`-v /path/to/your/music:/music` ：将你的音乐文件夹挂载到容器内的 `/music` 目录。请将 `/path/to/your/music` 替换为你实际的音乐文件夹路径

`-v lms-data:/var/lms` ：将 LMS 的数据库和配置文件持久化保存，确保容器重启后数据不丢失

### 通过 NAS 管理界面部署

如果你使用飞牛 NAS 或其他支持 Docker 的 NAS 设备：

1. 添加镜像：打开 NAS 的 Docker 管理界面，点击'本地镜像' → '添加镜像' → '从 URL 添加'

2. 输入镜像地址：填入 `epoupon/lms:latest`，等待镜像下载完成

3. 配置容器：

勾选'开机自动启动'确保服务重启后自动运行

端口映射：设置容器端口 `5082` 映射到主机端口（建议保持 `5082`，如有冲突可改为其他端口如 `8080`）

存储配置：

创建一个文件夹用于存储音乐文件，将其挂载到容器内的 `/music` 路径

创建一个文件夹用于存储 LMS 的数据库和配置，将其挂载到容器内的 `/var/lms` 路径

环境变量：通常无需设置特殊的环境变量，LMS 会使用默认配置

4. 启动容器：完成上述配置后，点击启动按钮即可

### 首次访问

容器启动成功后，打开浏览器访问 `http://你的NAS\_IP:5082`（如果改了端口号，请使用对应的端口）

首次访问时，LMS 会引导你创建管理员账户。设置好用户名和密码后，刷新页面即可进入登录界面。

登录后，你需要：

1. 在'管理'或'设置'中添加你的音乐库路径（指向你挂载的 `/music` 目录）

2. 根据需要调整扫描设置（扫描频率、标签分隔符等）

3. 启动初始扫描，LMS 会自动导入你的所有音乐文件

### 常见配置

**开启 Subsonic API**（用于兼容其他客户端）：

在 LMS 的管理界面中找到'Subsonic API'选项并启用

这样你就可以使用支持 Subsonic 协议的第三方客户端连接到 LMS

**音乐标签优化建议**：

LMS 最好与 MusicBrainz Picard 标签编辑器配合使用

确保 `artist` 标签包含单一的显示名称，`artists` 标签包含实际艺术家名称

对于多专辑艺术家，使用 `albumartists` 和 `albumartistssort` 标签效果更佳

**艺术家信息管理**：

可以在音乐库中创建 `ArtistInfo` 文件夹来存储艺术家元数据和图片

按照 Kodi 的标准格式组织，LMS 会自动识别并使用这些信息

到此为止，你已经成功部署了 LMS 音乐服务器！现在你可以尽情享受随处可得的个人音乐库体验。无论是在家还是在外，都能通过网页界面访问你精心收集的音乐。如有任何问题，可以前往 [项目讨论区](https://github.com/epoupon/lms/discussions) 或 [问题追踪器](https://github.com/epoupon/lms/issues) 寻求帮助。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/private-music-library-lms-server-remote-access>
