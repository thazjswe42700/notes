---
title: "自建RSS阅读聚合中心，内网穿透远程订阅，Selfoss极简Docker部署完全指南"
date: 2025-10-16
description: "Selfoss是一款功能强大的PHP开源RSS阅读器和内容聚合平台，支持集中管理来自各类网站、社交网络的更新内容。本文详细介绍了Selfoss的特性、Docker快速部署步骤，包括镜像配置、端口映射、数据存储、环境变量设置等内容，帮助用户轻松在NAS及其他设备上部署运行。"
canonical: https://www.passnat.com/posts/self-hosted-rss-reader-selfoss-docker-deployment-guide
---

## 项目介绍

Selfoss 是一款功能强大的 RSS 阅读器和内容聚合平台。它能够帮助你集中管理来自各类网站、社交网络和其他平台的更新内容，让你无需逐一访问各个网站就能掌握最新资讯。项目采用 PHP 编写，具有极强的兼容性和部署灵活性，几乎可以在任何支持 PHP 的服务器上运行。

无论你是新闻爱好者、内容创作者还是信息收集者，Selfoss 都能为你提供一个统一、高效的内容阅读中心。支持 OPML 导入导出，让你轻松迁移自己的订阅列表。

项目地址： [点我跳转](https://github.com/fossar/selfoss)

## 部署安装

如果需要在外网也能访问你的 Selfoss 服务，建议配置内网穿透功能。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的配置步骤可以 [参考这篇教程](https://www.passnat.com/posts/p)，里面有详细的全平台上手指南。

### 使用 Docker 快速部署

打开你的 NAS 设备或其他支持 Docker 的系统（如飞牛 NAS、群晖等）。

进入 Docker 管理界面，点击 **本地镜像** - **添加镜像** - **从 URL 添加**。

在镜像地址栏中输入以下镜像地址：

```
fossar/selfoss:latest
```

等待镜像下载完成后，点击 **启动** 按钮启动容器。

基础配置步骤：

1. 开机自动启动 - 勾选此选项后点击下一步，容器会在系统重启时自动启动

2. 端口设置 - 将容器内部的 80 端口映射到主机的某个端口（如 8080）

如果 8080 端口被占用，你可以修改为其他端口，如 8081、8082 等

容器内的 80 端口保持不变

3. 存储位置 - 创建一个本地文件夹用于存储 Selfoss 的数据

建议在 NAS 上创建一个名为 'selfoss-data' 的文件夹

将此文件夹映射到容器内的 '/var/www/html/data' 目录，用于保存数据库、缓存和日志

4. 环境变量设置（可选）

此项可以不填，使用默认配置即可正常运行

如需自定义，可根据项目需求后续在配置文件中修改

完成以上步骤后，点击启动容器。

访问应用：

容器启动成功后，在浏览器中输入以下地址即可访问：

```
http://你的NAS地址:8080
```

首次访问会进入初始化界面，按照提示完成基础配置即可开始使用。

如需添加 RSS 源，在应用内的设置中找到对应选项，输入 RSS 源地址即可订阅。支持批量导入 OPML 文件，方便快速迁移你的订阅列表。

本篇教程结束

祝你使用愉快！如有问题可访问 [官方论坛](https://forum.selfoss.aditu.de/) 获取帮助。

---

原文：<https://www.passnat.com/posts/self-hosted-rss-reader-selfoss-docker-deployment-guide>


## 相关文章

- [自建全文RSS提取服务 无需跳转看完整文章 Docker一键部署NAS内网穿透远程访问](full-text-rss-extraction-service-docker-nas-intranet-penetration.md)
- [自建 RSS 阅读器 FreshRSS：飞牛 NAS Docker 部署，关注的更新汇到一页，配内网穿透在外面也能刷](self-hosted-rss-reader-freshrss-nas-docker-deployment-intranet-penetration.md)
- [使用Morss完整抓取RSS全文内容，NAS Docker极速部署，内网穿透远程订阅](morss-nas-docker-rss-remote-subscription.md)
- [开源RSS处理管道系统，灵活转换聚合内容，NAS Docker快速部署，内网穿透远程访问](open-source-rss-processing-pipeline-flexible-content-aggregation-nas-docker-deployment-intranet-penetration.md)
- [自建 RSS 源 RSSHub：飞牛 NAS Docker 部署，给没有 RSS 的网站造一个订阅源，配内网穿透在外面也能刷](nas-rss-aggregation-service-docker-rsshub-intranet-penetration-remote-subscription.md)
