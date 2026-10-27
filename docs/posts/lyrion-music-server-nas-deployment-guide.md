---
title: "跨平台音乐服务器Lyrion Music Server部署指南，NAS音乐流媒体服务器快速安装教程"
date: 2025-10-16
description: "Lyrion Music Server 是功能强大的跨平台音乐流媒体服务器，支持本地音乐库管理、多种播放器和丰富插件生态。本教程详细介绍 LMS 在 Docker 环境下的快速部署方法，包括镜像配置、端口映射、存储设置等完整步骤，帮助用户轻松搭建个人音乐服务系统。"
canonical: https://www.passnat.com/posts/lyrion-music-server-nas-deployment-guide
---

## Lyrion Music Server 部署教程

#### 项目介绍

Lyrion Music Server（简称 LMS，原名 Logitech Mediaserver、SlimServer 等）是一款功能强大的音乐流媒体服务器软件。它专为 Logitech 旗下的 Squeezebox 系列音频播放器设计，同时也支持众多第三方软件播放器如 Squeezelite 和 SqueezePlay。

这是一个跨平台的音乐管理和播放解决方案。LMS 不仅能够管理和串流你的本地音乐库，还能通过丰富的插件生态系统接入各大音乐流媒体服务和网络电台。无论你是音乐发烧友还是普通用户，都能通过 LMS 在家中任意角落享受高品质音乐体验。

项目采用 Perl 语言开发，具有极强的兼容性，可运行在 Linux、macOS、Windows、Solaris 等几乎所有主流操作系统，甚至树莓派等单板计算机和 NAS 设备上也能流畅运行。

项目地址：[点我跳转](https://github.com/lms-community/slimserver)

#### 部署安装

如需在外部网络也能访问你的音乐服务器，建议配置内网穿透功能。推荐使用 [帕斯内网穿透](https://passnat.com) 服务，先注册账号备用。具体的操作指南可以 [参考这里](https://www.passnat.com/posts/p) 了解全平台内网穿透快速上手方案。

##### Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板

点击本地镜像 - 添加镜像 - 从 URL 添加，在镜像地址栏填入：

```
lmscommunity/logitechmediaserver:latest
```

等待镜像下载完成后，点击启动按钮创建容器。

**基础配置步骤：**

勾选开机自动启动选项，然后进入下一步。

**端口映射设置：**

Lyrion Music Server 需要以下端口映射：

9000：Web 管理界面和 API 接口（容器内端口 9000 → 主机端口 9000）

3483：CLI 协议端口，用于播放器通信（容器内端口 3483 → 主机端口 3483）

如果你的主机上这些端口已被占用，可以修改主机端口号（冒号左边的数字），但容器内端口（冒号右边的数字）需要保持不变。例如可以改为 9001:9000 或 3484:3483。

**存储配置：**

为了保证音乐库数据和配置的持久化，建议创建以下目录映射：

/music：存放你的音乐文件目录

/var/lib/logitechmediaserver：LMS 的配置和数据库文件目录

在 NAS 上创建相应的文件夹，然后映射到容器内的这些路径。

**环境变量设置：**

通常情况下无需额外配置环境变量，使用默认设置即可正常运行。如有特殊需求，可根据官方文档进行自定义配置。

配置完成后点击启动，容器将自动运行。

**访问服务：**

部署完成后，在浏览器中访问 http://你的设备IP:9000 即可进入 Lyrion Music Server 的 Web 管理界面。首次访问会进行初始化配置，按照提示添加你的音乐库路径即可开始使用。

本篇教程结束

---

原文：<https://www.passnat.com/posts/lyrion-music-server-nas-deployment-guide>
