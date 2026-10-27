---
title: "自建漫画阅读器Teemii极简部署，NAS一键启动，内网穿透随处看漫画"
date: 2025-10-16
description: "Teemii是自托管漫画阅读器与管理平台，基于Vue.js和Node.js构建。支持CBZ、CBR等多种格式，提供元数据聚合、阅读进度追踪、Kitsu和AniList同步功能。集个性化推荐、跨平台部署于一身，让你轻松管理漫画库，随时随地畅享精彩内容。"
canonical: https://www.passnat.com/posts/teemii-comic-reader-nas-deployment
---

## 项目介绍

Teemii 是一款功能强大的自托管漫画阅读器和管理平台。基于 Vue.js 和 Node.js 构建，它为漫画爱好者提供了一个简洁高效的阅读和收藏管理解决方案。

项目支持跨平台部署，用户可以在浏览器中直接阅读漫画，无需额外软件。Teemii 集成了强大的元数据聚合功能，支持 CBZ 和 CBR 等多种漫画格式，能够自动追踪阅读进度，并与 Kitsu、AniList 等平台同步你的阅读记录。同时，项目还提供了个性化漫画推荐功能，可以根据你的阅读习惯推荐新作品。

无论是自建漫画库还是从多个在线资源获取章节，Teemii 都能轻松胜任，让你随时随地畅享精彩漫画内容。

项目地址：[点我跳转](https://github.com/dokkaner/teemii)

## 部署安装

如果你需要在外网也能访问 Teemii，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，具体的配置步骤可以 [查看这里](https://www.passnat.com/posts/p)，帮助你快速上手全平台内网穿透方案。

### 使用 Docker Compose 快速部署

这是最简单快速的部署方式，适合大多数用户。

**第一步：复制配置文件**

将以下配置保存为 'docker-compose.yml' 文件：

```yaml
version: '3.8'

services:
  teemii:
    image: dokkaner/teemii:latest
    container_name: teemii
    ports:
      - "3000:3000"
    volumes:
      - teemii_data:/app/data
      - teemii_manga:/app/manga
    environment:
      - NODE_ENV=production
    restart: unless-stopped

volumes:
  teemii_data:
  teemii_manga:
```

**第二步：在 NAS 设备上部署**

打开你的飞牛 NAS 或其他 NAS 设备，进入 Docker 管理界面。

**第三步：创建容器**

点击 'Docker Compose' 或 '从文件导入'

将上面的配置文件内容粘贴进去

点击 '部署' 或 '启动'

**第四步：端口配置**

默认配置使用 '3000' 端口。如果你的系统中 3000 端口已被占用，可以修改上面配置中的第一个 '3000' 为其他端口号（如 '8080:3000'），这样就可以通过 'http://ip:8080' 访问。容器内部的端口 '3000' 保持不变。

**第五步：存储配置**

配置文件中已经为你设置了两个持久化存储目录：

'teemii\_data'：用于存储应用数据和配置信息

'teemii\_manga'：用于存储你的漫画库文件

这些目录会自动创建并保持数据持久化。

**第六步：环境变量说明**

当前配置使用的环境变量：

变量名 | 说明 | 默认值 | 是否必须

NODE\_ENV | 运行环境 | production | 否

其他环境变量均使用默认配置，无需额外设置。

**第七步：启动完成**

部署成功后，打开浏览器访问 'http://你的NAS\_IP:3000' 即可进入 Teemii 界面。

首次访问时，你可以开始添加漫画库、配置元数据源，以及同步你的阅读进度到 Kitsu 或 AniList 等平台。

## 总结

通过以上步骤，你已经成功部署了 Teemii 漫画阅读器。现在你可以在自己的 NAS 上建立一个私人的漫画管理平台，随时随地享受阅读体验。如有任何问题，可以查阅官方文档或在项目的 GitHub Discussions 中寻求帮助。

祝你阅读愉快！

---

原文：<https://www.passnat.com/posts/teemii-comic-reader-nas-deployment>
