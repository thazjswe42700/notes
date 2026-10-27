---
title: "自建网络电台完整方案 AzuraCast Docker 快速部署指南 NAS 轻松搭建"
date: 2025-10-16
description: "AzuraCast是自托管网络电台管理套件，提供开箱即用的完整广播解决方案。集成电台管理、音乐上传、直播流媒体、听众统计等功能。低配置要求，开源免费，数据完全由您掌控。支持Docker快速部署，适合个人爱好者和专业运营者快速搭建功能完整的网络电台。"
canonical: https://www.passnat.com/posts/azuracast-docker-nas-wangluo-diantai-kuaisushubu
---

## 项目介绍

AzuraCast 是一个功能强大的自托管网络电台管理套件，为您提供开箱即用的完整广播解决方案。基于现代化的网络技术栈构建，它集成了电台管理、音乐上传、直播流媒体、听众统计等多项功能于一身。

无论您是个人爱好者还是专业电台运营者，AzuraCast 都能让您在几分钟内快速搭建一个功能完整的网络电台。它对服务器配置要求低，即使在最经济的 VPS 主机上也能流畅运行。项目采用开源协议，完全免费，所有数据掌握在您自己手中。

通过直观的网页界面，您可以轻松管理电台的方方面面——上传音乐库、安排播放列表、监控听众数据、管理 DJ 账号，一切尽在掌握。

项目地址：[点我跳转](https://github.com/AzuraCast/AzuraCast)

## 部署安装

如果您需要在外网也能访问自己的电台，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即使在办公室或出差时也能随时管理您的电台。具体操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 帕斯内网穿透的全平台使用攻略。

### Docker 快速部署

打开您的 NAS 设备或 Linux 服务器，进入本地镜像管理界面，选择「从 URL 添加」选项。

在镜像地址栏中填入：ghcr.io/azuracast/azuracast:latest

等待镜像下载完成后，点击启动按钮创建容器。

**端口映射配置：**

AzuraCast 需要映射以下主要端口：

80 - HTTP 网页界面访问端口

443 - HTTPS 安全访问端口

2022 - SFTP 文件传输端口

8000-8496 - 多个音频流媒体端口（用于支持多个电台）

如果您的系统中某些端口已被占用，可以修改主机端口（冒号前面的数字），但容器内部端口（冒号后面的数字）需要保持不变以确保正常运行。

**存储位置配置：**

AzuraCast 需要持久化存储以下数据目录，建议提前在 NAS 中创建相应文件夹进行映射：

/var/azuracast/stations - 电台数据和配置

/var/azuracast/backups - 备份文件存储

/var/lib/mysql - 数据库文件

/var/azuracast/storage/uploads - 上传的音乐和文件

**环境变量设置：**

必须设置：

AZURACAST\_VERSION - AzuraCast 版本号（默认：latest）

AZURACAST\_HTTP\_PORT - HTTP 访问端口（默认：80）

AZURACAST\_HTTPS\_PORT - HTTPS 访问端口（默认：443）

AZURACAST\_SFTP\_PORT - SFTP 端口（默认：2022）

其他配置项可选，一般情况下无需修改，直接使用默认值即可。

配置完成后点击启动，容器将自动初始化所有必要的服务。

**访问您的电台：**

部署完成后，在浏览器中访问 http://您的服务器IP 即可进入 AzuraCast 的管理后台。首次访问会提示创建管理员账号，按照提示完成初始化即可开始使用。

本篇教程结束

---

原文：<https://www.passnat.com/posts/azuracast-docker-nas-wangluo-diantai-kuaisushubu>
