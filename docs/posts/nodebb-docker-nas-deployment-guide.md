---
title: "NodeBB现代论坛社区系统Docker部署指南，NAS一键安装配置实时互动讨论社区平台"
date: 2025-10-16
description: "NodeBB是现代化论坛社区软件，由Node.js驱动，支持Redis、MongoDB、PostgreSQL数据库。采用WebSocket实现实时互动，融合响应式设计和RESTful API，保留传统论坛特性。支持插件扩展，满足个人社区到企业级论坛需求。"
canonical: https://www.passnat.com/posts/nodebb-docker-nas-deployment-guide
---

## 项目介绍

NodeBB 是一款现代化的论坛社区软件，由 Node.js 驱动，支持 Redis、MongoDB 或 PostgreSQL 数据库。它采用 WebSocket 技术实现实时互动和消息推送，融合了当代网络的最佳实践：实时流式讨论、响应式移动设计和丰富的 RESTful API，同时保留了传统论坛的经典特性——分类层级、本地用户账户和异步消息系统。

NodeBB 内核提供基础功能，通过第三方插件系统可扩展更多高级功能和集成能力。无论是个人社区还是企业级论坛，NodeBB 都能满足你的需求。

项目地址：[点我跳转](https://github.com/NodeBB/NodeBB)

## 部署安装

若要在公网环境下访问你的论坛社区，建议配置内网穿透服务。你可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，轻松实现远程访问。详细操作指南请 [点击查看](https://www.passnat.com/posts/p)，帮助你快速上手全平台内网穿透方案。

### Docker Compose 快速部署

NodeBB 官方推荐使用 Docker Compose 进行一键部署，包含完整的数据库配置。

复制下列配置文件到你的 NAS 或服务器，保存为 **docker-compose.yml**：

```yaml
services:
  nodebb:
    image: ghcr.io/nodebb/nodebb:latest
    restart: unless-stopped
    ports:
      - '4567:4567'
    volumes:
      - nodebb-build:/usr/src/app/build
      - nodebb-uploads:/usr/src/app/public/uploads
      - nodebb-config:/opt/config

  mongo:
    image: 'mongo:7-jammy'
    restart: unless-stopped
    environment:
      MONGO_INITDB_ROOT_USERNAME: nodebb
      MONGO_INITDB_ROOT_PASSWORD: nodebb
      MONGO_INITDB_DATABASE: nodebb
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:
  nodebb-build:
  nodebb-uploads:
  nodebb-config:
```

打开飞牛 NAS 或其他 NAS 设备，进入 Docker 管理界面。

点击 **本地镜像** → **添加镜像** → **从 URL 添加**，在镜像地址栏填入：

```
ghcr.io/nodebb/nodebb:latest
```

同时添加 MongoDB 镜像：

```
mongo:7-jammy
```

等待两个镜像下载完成。

### 配置说明

#### 端口映射

NodeBB 默认运行在容器内的 **4567** 端口。在 docker-compose 配置中，**4567:4567** 表示将容器内的 4567 端口映射到主机的 4567 端口。如果你的主机上 4567 端口已被占用，可以修改前面的数字（如改为 **8080:4567**），但容器内的端口需保持 **4567** 不变。

#### 存储挂载

为确保数据持久化，建议在 NAS 上创建以下文件夹用于数据存储：

nodebb-build - 应用构建文件
nodebb-uploads - 用户上传的文件（头像、附件等）
nodebb-config - 配置文件
mongo-data - MongoDB 数据库数据

#### 环境变量设置

NodeBB 的环境变量已在镜像中预设，通常无需额外配置。如有特殊需求，可添加以下可选变量：

必须设置：

MONGO\_INITDB\_ROOT\_USERNAME: nodebb - MongoDB 管理员用户名
MONGO\_INITDB\_ROOT\_PASSWORD: nodebb - MongoDB 管理员密码（建议修改为强密码）

可选设置：

NODE\_ENV: production - 生产环境模式
DAEMON: false - 后台运行模式

其他变量保持默认即可，直接启动容器。

### 启动应用

所有配置完成后，点击启动按钮。容器启动通常需要 1-2 分钟完成初始化。

启动成功后，在浏览器中访问：

```
http://你的NAS_IP:4567
```

首次访问时，NodeBB 会引导你完成初始化设置，包括管理员账号创建、论坛名称配置等。按照向导步骤完成即可。

到这里，你的 NodeBB 论坛社区就已经成功部署并正常运行了！

本篇教程结束

---

原文：<https://www.passnat.com/posts/nodebb-docker-nas-deployment-guide>


## 相关文章

- [WhoDB轻量级数据库管理工具，自然语言对话查询，NAS极简部署，无公网内网穿透远程访问。](whodb-lightweight-database-management-natural-language-query-nas-simple-deployment-private-network-remote-access.md)
- [开源低代码平台Budibase快速部署指南，Docker极简搭建应用，内网穿透远程访问](open-source-low-code-platform-budibase-quick-deployment-guide-docker-simple-setup-internal-network-penetration-remote-access.md)
- [私有Beancount记账系统Docker快速部署指南，NAS轻松搭建个人财务管理平台](private-beancount-accounting-docker-deployment-guide-nas-personal-finance-management.md)
- [无代码可视化爬虫平台 Spider-Flow，流程图定义任务无需编程，NAS 一键部署内网穿透远程访问](no-code-web-scraper-spider-flow-flowchart-nas-deployment.md)
- [自建私人API开发环境，Yaade开源协作平台Docker快速部署指南](yaade-open-source-api-development-docker-deployment-guide.md)
