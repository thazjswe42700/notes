---
title: "私有社区服务器Circled.me极简部署指南，NAS一键启动无需公网穿透"
date: 2025-10-16
description: "探索 Circled.me Community Server，一个基于 Go 语言的私有社区服务器解决方案。支持照片视频备份、音视频通话、实时聊天、人脸识别等功能。所有数据完全私密可控，资源占用极低。本教程详细介绍了在 NAS 设备上的快速部署方法、容器配置要点及环境变量设置，帮助您轻松搭建安全的数字家园。"
canonical: https://www.passnat.com/posts/circled-me-private-community-server-simple-deployment-guide-nas-one-click-startup-no-public-network-penetration
---

## 项目介绍

Circled.me Community Server 是一个功能强大的私有社区服务器，致力于帮助用户在自己的服务器上轻松备份、分享照片、视频和相册，同时支持音视频通话和即时聊天功能。最重要的是，所有数据始终保持私密，完全由您掌控。

项目采用 Go 语言开发，具有出色的性能表现和极低的资源占用，无需依赖任何第三方服务。无论是家庭成员之间分享生活点滴，还是兴趣社群进行交流互动，Circled.me 都能为您提供一个安全、私密的数字家园。

**核心功能包括：**

📱 iOS/Android 应用自动备份照片和视频

🎬 支持本地存储或 S3 兼容存储方案

📞 浏览器和移动应用内的音视频通话

🤖 智能人脸识别和标签功能

💬 支持推送通知的实时聊天

🗺️ 地理位置反向编码和自动分组

🎞️ 视频自动转码为 H.264 网页兼容格式

项目地址：[点我跳转](https://github.com/circled-me/server)

## 部署安装

如果您想在外网也能访问您的私有服务器，建议配置内网穿透服务。您可以选择 [帕斯内网穿透](https://passnat.com)

这样的专业方案，先行注册账号以备使用。具体的配置步骤可以参考 [这份详细教程](https://www.passnat.com/posts/p)

，涵盖了全平台的上手指南。

### 快速部署方式

打开您的飞牛 NAS 或其他 NAS 设备管理面板。

进入 **本地镜像** 模块，点击 **添加镜像** - **从 URL 添加**。

在镜像地址栏填入：`gubble/circled-server:latest`

等待镜像下载完成，点击启动按钮启动容器。

### 容器配置说明

#### 开机自动启动设置

勾选 **开机自动启动** 选项，确保 NAS 重启后服务自动运行。

#### 端口映射配置

容器内部运行在 **8080** 端口，您需要将其映射到主机端口。建议保持为 `8080:8080`，如果该端口已被占用，可修改前面的数字（如 `8081:8080`），但容器内的端口务必保持为 **8080**。

#### 存储卷配置

Circled.me 需要持久化存储来保存数据库和用户上传的媒体文件。请在 NAS 上创建一个专用文件夹（如 `/mnt/data1`），然后将其映射到容器内的 `/mnt/data1` 路径。这样即使容器重启，您的所有数据也会完整保留。

#### 环境变量设置

以下是推荐的环境变量配置：

**必须设置：**

`SQLITE\_FILE`: `/mnt/data1/circled.db` （数据库文件位置）

`BIND\_ADDRESS`: `0.0.0.0:8080` （绑定地址）

`DEFAULT\_BUCKET\_DIR`: `/mnt/data1` （默认存储目录）

**可选设置：**

`FACE\_DETECT`: `yes` （启用人脸识别，默认开启）

`FACE\_DETECT\_CNN`: `no` （使用 CNN 算法进行更精准的人脸检测，但速度较慢）

`DEBUG\_MODE`: `yes` （调试模式）

`PUSH\_SERVER`: `https://push.circled.me` （推送服务器地址）

`TURN\_SERVER\_IP`: 您的公网 IP （如需配置视频通话的 TURN 服务器）

`TURN\_SERVER\_PORT`: `3478` （TURN 服务端口，UDP 协议）

其他变量保持默认即可，无需额外配置。

### 启动服务

完成上述配置后，点击 **启动** 按钮。

容器启动成功后，您可以在浏览器中访问 `http://您的NAS\_IP:8080` 来打开 Circled.me 服务。

首次访问时，系统会引导您创建管理员账户，完成注册后即可开始使用。

### 关于移动应用

Circled.me 的完整功能需要通过官方移动应用来体验，包括照片自动备份、视频通话等功能。您可以在 [官方网站](https://circled.me)

下载 iOS 或 Android 版本的应用，或访问 [项目源码仓库](https://github.com/circled-me/app)

了解更多信息。

本篇教程结束

---

原文：<https://www.passnat.com/posts/circled-me-private-community-server-simple-deployment-guide-nas-one-click-startup-no-public-network-penetration>
