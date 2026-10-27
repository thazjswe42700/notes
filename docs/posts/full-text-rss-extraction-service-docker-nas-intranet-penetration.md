---
title: "自建全文RSS提取服务 无需跳转看完整文章 Docker一键部署NAS内网穿透远程访问"
date: 2025-10-16
description: "Full-Text RSS 是一款强大的全文提取服务，自动抓取网页文章完整内容并转换为 RSS 源。基于 PHP 构建，提供 Web 界面，支持摘要转全文、文章提取等功能。通过 Docker 快速部署到 NAS 或服务器，配合内网穿透实现远程访问。让你在 RSS 阅读器中直接阅读完整文章，无需跳转原网站。"
canonical: https://www.passnat.com/posts/full-text-rss-extraction-service-docker-nas-intranet-penetration
---

## 项目介绍

Full-Text RSS 是一个功能强大的 RSS 全文提取服务，它能够自动抓取网页文章的完整内容，并将其转换为全文 RSS 订阅源。无论是摘要式 RSS 还是完整文章，都能通过这个服务获取到全部内容，让你在 RSS 阅读器中就能看到完整的文章正文，无需跳转到原网站。

这个项目特别适合那些想要聚合多个信息源、统一阅读体验的用户。基于 PHP 构建，提供了开箱即用的 Web 界面，支持文章提取和 RSS 源转换两大核心功能。

项目地址：[点我跳转](https://github.com/heussd/fivefilters-full-text-rss-docker)

## 部署安装

如果你需要在外网也能访问这个服务，建议配置内网穿透来实现远程访问。可以前往[帕斯内网穿透](https://passnat.com)注册账号，这样就能随时随地使用你的 RSS 服务了。具体的操作步骤可以[参考这篇教程](https://www.passnat.com/posts/p)了解全平台内网穿透的快速上手方法。

### Docker 快速部署

打开你的 NAS 设备或服务器的 Docker 管理界面。

点击**本地镜像**- **添加镜像**- **从 URL 添加**，在镜像地址栏中填入：

```
heussd/fivefilters-full-text-rss:latest
```

等待镜像下载完成后，点击**启动**按钮启动容器。

### 容器配置

**勾选开机自启动**，然后进入下一步进行端口设置。

**端口映射设置：**

容器内部端口：80

宿主机端口：80（如果你的 NAS 或服务器上 80 端口已被占用，可以修改为其他端口，比如 8080、3000 等，只需确保格式正确即可）

**存储位置设置：**

这个应用需要缓存 RSS 数据，请在你的 NAS 中创建一个文件夹（比如命名为 'rss-cache'），然后将其映射到容器内的 '/var/www/html/cache/rss' 路径。这样可以确保重启容器后缓存数据不会丢失。

**环境变量设置：**

选择设置，可以不填

'FTR\_ADMIN\_PASSWORD'：管理员密码（留空表示禁用管理后台，如需使用后台管理功能请设置一个密码）

其他配置保持默认即可，直接点击**启动**完成部署。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS地址:80
```

（如果你修改了端口，请将 80 替换为你设置的端口号）

此时你就能看到 Full-Text RSS 的 Web 界面了。你可以在这里输入任何 RSS 源的 URL，服务会自动提取其中文章的完整内容，或者直接输入网页 URL 来提取单篇文章的全文。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/full-text-rss-extraction-service-docker-nas-intranet-penetration>
