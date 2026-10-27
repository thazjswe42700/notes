---
title: "自建私人日历通讯录服务器Baïkal，Docker极简部署，完全私密掌控，内网穿透远程访问。"
date: 2025-10-16
description: "Baïkal 是轻量级 CalDAV 和 CardDAV 服务器，提供完善的网页管理界面。支持 MySQL 和 SQLite 数据库，PHP 开发，兼容 iOS、macOS、Android 和 Thunderbird。通过 Docker 快速部署，在自己的服务器上私密托管日历和联系人，完全保护个人隐私和数据安全。"
canonical: https://www.passnat.com/posts/baical-docker-private-calendar-contacts-server
---

## 项目介绍

Baïkal 是一个轻量级的 CalDAV 和 CardDAV 服务器，专为保护个人隐私而设计。它提供了一个功能完善的网页管理界面，让用户能够轻松管理用户账户、通讯录和日历。无论是个人用户还是小型组织，都可以通过 Baïkal 在自己的服务器上托管日历和联系人数据，避免依赖第三方云服务。

Baïkal 采用 PHP 开发，支持 MySQL 和 SQLite 数据库，安装部署简单快速，只需要一台支持基础 PHP 的服务器即可运行。它完全兼容 iOS、macOS、Android（DAVx5）、Mozilla Thunderbird 等众多 CalDAV 和 CardDAV 客户端应用。通过自建 Baïkal 服务器，你可以在任何设备上无缝访问你的日历和联系人，同时保持数据完全在自己的掌控之中。

项目地址：[点我跳转](https://github.com/sabre-io/Baikal)

## 部署安装

若想在外网也能访问你的 Baïkal 日历服务器，建议配置内网穿透工具以安全地暴露服务。可以前往[帕斯内网穿透](https://passnat.com)注册账号并按照[全平台上手攻略](https://www.passnat.com/posts/p)进行配置，这样就能在任何地方访问你的私人日历和通讯录了。

### 使用 Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板，进入本地镜像管理模块，选择从 URL 添加镜像的选项。

在镜像地址栏填入：ckulka/baikal:latest

等待镜像下载完成后，点击启动按钮创建容器。

**端口配置**

在容器端口设置中，将容器内的 80 端口映射到宿主机的任意可用端口（比如 8080）。如果 8080 端口已被占用，你可以修改为其他端口号，但容器内的端口保持 80 不变。

**存储配置**

Baïkal 需要持久化存储来保存日历、联系人和配置数据。在存储位置设置中，建议创建一个本地文件夹（比如 `/baikal/data`），将其映射到容器内的 `/var/www/html/Specific` 目录，这样即使容器重启也不会丢失数据。

**环境变量设置**

Baïkal 的 Docker 镜像通常无需额外的环境变量配置，保持默认即可。如果镜像支持以下选项，可根据需要设置：

TZ: 时区设置（可选，默认 UTC）

ADMIN\_LOGIN: 管理员用户名（可选，默认为 admin）

ADMIN\_PASSWORD: 管理员密码（可选，需要自行设置）

其他设置保持默认，勾选开机自动启动后，点击启动容器。

**访问应用**

容器启动成功后，在浏览器中访问 `http://你的设备IP:8080/html/`（将 8080 替换为你实际映射的端口），即可看到 Baïkal 的管理界面。

首次访问时会进入安装向导，按照提示完成数据库配置和管理员账号设置，之后就可以开始管理你的日历和联系人了。

本篇教程到此结束。现在你已经拥有了一个完全私有的日历和通讯录服务器，所有数据都掌握在自己手中。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/baical-docker-private-calendar-contacts-server>


## 相关文章

- [单文件PHP数据库管理工具Adminer，支持MySQL、PostgreSQL等多种数据库，NAS极简部署内网穿透远程管理](adminer-php-database-management-tool-mysql-postgresql-nas-deployment-internal-penetration-remote-management.md)
- [轻量级PHP博客系统Typecho在NAS上Docker部署，内网穿透远程访问详细教程指南](typecho-nas-docker-deployment-internal-penetration-remote-access.md)
- [AgenDAV 网页日历客户端 NAS 自建部署指南，内网穿透远程访问日程管理系统](agendav-nas-deployment-guide-calendar-scheduling.md)
- [私有部署开源聊天应用VoceChat，NAS轻松搭建安全私密聊天系统，内网穿透远程访问](vocechat-private-deployment-nas-internal-network-penetration.md)
- [自建音乐流媒体服务器Polaris，免费开源支持多格式，NAS一键部署内网穿透远程听歌](polaris-open-source-music-server-nas-remote-streaming.md)
