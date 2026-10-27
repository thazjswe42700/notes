---
title: "私人云盘Filestash部署完全指南，NAS一键Docker启动，支持多云存储聚合管理"
date: 2025-10-16
description: "Filestash是功能强大的网页文件管理器，支持FTP、SFTP、WebDAV、S3、Google Drive、Dropbox等多种存储后端，提供统一的文件管理界面。已开发8年+，稳定可靠，支持桌面移动端，内置音视图查看器、转码、Chromecast投屏和共享链接功能，高度可扩展，Docker一键部署。"
canonical: https://www.passnat.com/posts/private-cloud-storage-filestash-deployment-guide-nas-docker-one-click-multi-cloud-storage-aggregation
---

## 项目介绍

Filestash 是一个开箱即用的、功能强大的网页文件管理器，它让你可以在浏览器中统一管理存储在各个位置的数据。无论你的文件存储在 FTP、SFTP、WebDAV、S3、Google Drive、Dropbox 还是其他云服务中，Filestash 都能将它们聚合到一个简洁美观的界面中。

这是一个已经开发了 8 年以上的成熟项目，具有极强的稳定性和可靠性。界面设计精美流畅，支持桌面端和移动端，无论你在哪里都能随时访问和管理你的文件。项目高度可扩展，通过丰富的插件生态系统，你可以根据需求定制功能。它内置了音乐、视频、图片查看器，支持转码和 Chromecast 投屏，还支持创建共享链接并挂载为本地网络驱动器。

项目地址： [点我跳转](https://github.com/mickael-kerjean/filestash)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即使在办公室或出差时也能随时访问家中的文件管理系统。具体的配置步骤可以 [参考这份完整攻略](https://www.passnat.com/posts/p)，包含了全平台的详细说明。

Filestash 提供了最简便的部署方式，只需一条 Docker 命令即可快速启动：

打开飞牛 NAS 或其他的 NAS 设备，进入 Docker 管理界面。

点击本地镜像 - 添加镜像 - 从 URL 添加。

在镜像地址栏填入：machines/filestash:latest

等待镜像下载完成后，点击启动按钮启动容器。

勾选开机自动启动选项，然后进入下一步。

**端口设置**：这里需要配置容器端口映射。Filestash 默认运行在容器内的 8334 端口，你可以将其映射到宿主机的任意端口。例如可以映射为 8334:8334（宿主机端口:容器内端口）。如果提示端口被占用，只需修改前面的数字即可，比如改为 8335:8334，这样就能通过 http://你的IP:8335 访问了。

**存储位置**：Filestash 会在 /data 目录下存储配置文件和缓存数据。建议在 NAS 上创建一个文件夹（例如 /mnt/filestash\_data），然后将其映射到容器内的 /data 目录，确保重启容器后数据不会丢失。

**环境变量设置**：

必须设置

FILESTASH\_ADMIN\_PASSWORD：设置管理员登录密码（用于初始化配置）

选择设置，可以不填

FILESTASH\_LOG\_LEVEL：日志级别，默认为 info

FILESTASH\_MAX\_UPLOAD\_SIZE：单个文件最大上传大小（单位字节），默认为 10GB

其他的都不用填写，直接下一步启动就好了。

创建成功后可以访问 http://你的NAS\_IP:8334

首次访问时，系统会要求你设置管理员密码。设置完成后，你就可以在配置界面添加各种存储后端（FTP、S3、WebDAV 等），然后就能在统一的界面中管理所有文件了。

到这里 Filestash 的部署就完成了。你现在拥有了一个功能完整的私人云盘系统，可以像使用 Dropbox 或 Google Drive 一样管理你的所有文件。

本篇教程结束

---

原文：<https://www.passnat.com/posts/private-cloud-storage-filestash-deployment-guide-nas-docker-one-click-multi-cloud-storage-aggregation>
