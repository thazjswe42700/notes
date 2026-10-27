---
title: "可视化Docker Compose管理工具，NAS极简部署Compose Craft，无公网内网穿透远程访问"
date: 2025-10-16
description: "Compose Craft是一款功能强大的Docker Compose可视化管理工具，通过图形化界面轻松创建、编辑和分享Docker配置文件。基于Next.js和TypeScript构建，集成React Flow节点式图形和MongoDB数据库。支持拖拽设计容器架构、一键分享配置、导入编辑现有Docker文件。本文详细讲解NAS部署安装步骤、环境变量配置和端口映射方法，帮助初学者和资深用户快速上手。"
canonical: https://www.passnat.com/posts/compose-craft-docker-visualization-nas-deployment-internal-network-remote-access
---

## 项目介绍

Compose Craft 是一款功能强大的 Docker Compose 可视化管理工具，它通过图形化界面让你轻松创建、编辑和分享 Docker Compose 配置文件。无论你是 Docker 初学者还是资深用户，都能通过这个工具大幅提升工作效率。

该项目基于 Next.js + TypeScript 构建，集成了 React Flow 节点式图形界面和 MongoDB 数据库。你可以通过直观的拖拽操作设计容器架构，一键分享配置图表，或导入现有的 Docker 文件进行编辑和导出。无论是本地部署还是云端使用，Compose Craft 都能为你的容器编排工作带来全新体验。

项目地址：[点我跳转](https://github.com/composecraft/composecraft)

## 部署安装

如果你需要在外网环境中访问自托管的 Compose Craft，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com) 来实现远程访问，先注册账号备用。具体的操作步骤可以参考 [这份详细攻略](https://www.passnat.com/posts/p)，帮助你快速上手各平台的内网穿透配置。

### 使用 Docker Compose 快速部署

这是最推荐的部署方式，包含了应用和数据库的完整配置。

**第一步：获取配置文件**

复制下列的 Docker Compose 配置文件：

```yaml
name: compose-craft
services:
  saas:
    image: composecraft/composecraft:latest
    ports:
      - 3000:3000
    environment:
      - CORE_ONLY=true
      - URL=http://localhost:3000
      - SECRET_KEY=changeme
      - MONGODB_URI=mongodb://dev:dev@db
    depends_on:
      - db
    networks:
      - internal
  db:
    image: mongo:latest
    environment:
      - MONGO_INITDB_ROOT_USERNAME=dev
      - MONGO_INITDB_ROOT_PASSWORD=dev
    ports:
      - 27017:27017
    networks:
      - internal
    volumes:
      - mongo_data:/data/db
networks:
  internal:
    driver: bridge
    attachable: false
    external: false
volumes:
  mongo_data:
    driver: local
    external: false
```

**第二步：部署到 NAS**

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理模块。

**第三步：导入配置**

点击'本地镜像' - '添加镜像' - '从 URL 添加'，在镜像字段中填入 composecraft/composecraft:latest，等待镜像下载完成。

**第四步：启动容器**

下载完成后，点击启动按钮启动容器。建议勾选'开机自动启动'，这样重启 NAS 后应用会自动运行。

**第五步：端口配置**

应用需要映射以下端口：

3000 → 3000（应用访问端口）

27017 → 27017（MongoDB 数据库端口，仅内部使用）

如果这些端口在你的 NAS 上已被占用，可以修改左侧的宿主机端口号（冒号前的数字），但容器内的端口（冒号后的数字）需要保持不变。

**第六步：环境变量设置**

环境变量设置说明：

**必须修改的环境变量：**

SECRET\_KEY：用于 JWT 令牌签名的密钥，请将 changeme 替换为你自己的安全密钥

**可选配置的环境变量：**

URL：应用的基础 URL，用于重定向和公开链接生成，默认为 http://localhost:3000

MONGO\_INITDB\_ROOT\_USERNAME：MongoDB 管理员用户名，默认为 dev

MONGO\_INITDB\_ROOT\_PASSWORD：MongoDB 管理员密码，默认为 dev

CORE\_ONLY：设置为 true 禁用 SaaS 托管功能，仅保留核心自托管功能

DISABLE\_TELEMETRY：设置为 true 关闭遥测数据收集

**第七步：存储配置**

MongoDB 数据库需要持久化存储。在 NAS 中创建一个文件夹（如 /data/mongo）用于存储数据库文件，将其映射到容器内的 /data/db 路径，确保重启后数据不会丢失。

**第八步：启动应用**

完成上述配置后，点击'下一步'启动容器。

**第九步：访问应用**

容器启动成功后，在浏览器中访问 http://你的NAS地址:3000 即可打开 Compose Craft 应用。你现在可以开始使用图形化界面来创建、管理和分享你的 Docker Compose 配置了。

本篇教程到此结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/compose-craft-docker-visualization-nas-deployment-internal-network-remote-access>


## 相关文章

- [现代React推箱子游戏，500+谜题无限撤销，NAS极简部署内网穿透在线玩](modern-react-sokoban-game-500-puzzles-unlimited-undo-nas-simple-deployment-intranet-penetration-online-play.md)
- [NextBeats复古Lofi音乐播放器部署指南，Docker一键启动，YouTube集成播放，音效混合定制，NAS内网穿透远程访问](nextbeats-retro-lofi-music-player-docker-deployment-guide-youtube-integration-sound-mixing-nas-remote-access.md)
- [开源日程管理系统Cal.com自建部署，完全控制数据NAS本地托管，支持内网穿透远程访问](open-source-calendar-management-cal-com-self-hosted-deployment-local-nas-storage-intranet-penetration-remote-access.md)
- [无代码可视化爬虫平台 Spider-Flow，流程图定义任务无需编程，NAS 一键部署内网穿透远程访问](no-code-web-scraper-spider-flow-flowchart-nas-deployment.md)
- [开源日程管理系统Cal.com自托管部署指南，NAS极简配置内网穿透远程访问](open-source-calendar-management-system-cal-com-self-hosted-deployment-guide-nas-simple-configuration-intranet-penetration-remote-access.md)
