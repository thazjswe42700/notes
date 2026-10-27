---
title: "自托管URL缩短器Pckd：免费开源短链服务，NAS一键部署快速追踪链接数据"
date: 2025-10-16
description: "自托管 URL 缩短器 Pckd 完全免费开源，采用 React、GraphQL、Prisma 等现代技术栈构建。支持 MySQL、PostgreSQL、MongoDB 多种数据库，可自定义短链前缀、实时追踪点击数据、获取访客 IP 地址及地理位置信息。重定向速度快至 150ms，包含用户注册、账户管理、链接收藏等完整功能。无论个人博客还是企业营销，都是打造个性化短链服务的理想选择。"
canonical: https://www.passnat.com/posts/pckd-self-hosted-url-shortener-free-open-source-short-link-service-nas-one-click-deployment-fast-tracking-link-data
---

## 项目介绍

Pckd 是一个功能强大的自托管 URL 缩短器，完全免费开源。它采用现代化技术栈构建，包括 React JS 前端、Apollo GraphQL Server 后端和 Prisma ORM，支持 MySQL、PostgreSQL、MongoDB 等多种数据库。

与市面上的付费 URL 缩短服务不同，Pckd 让你拥有完全的自主权。你可以在自己的域名上部署，自定义短链前缀，实时追踪每个链接的点击情况，获取访问者的 IP 地址、地理位置、设备信息、浏览器版本等详细数据。支持用户注册、账户管理、链接收藏等功能，而且重定向速度快至 150ms，安全可靠。

无论是个人博主、企业营销还是数据分析师，Pckd 都是打造个性化 URL 缩短服务的最佳选择。

项目地址： [点我跳转](https://github.com/Just-Moh-it/Pckd)

## 部署安装

若想在外网也能访问你的 Pckd 实例，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地使用你的短链服务了。具体操作步骤可以参考 [这份完整教程](https://www.passnat.com/posts/p)。

### 使用 Docker-Compose 快速部署

这是最推荐的部署方式，适合所有用户。

第一步：复制配置文件

将以下 Docker-Compose 配置保存为 `docker-compose.yml` 文件：

```yaml
version: "3"

services:
  server:
    build:
      context: ./server
    volumes:
      - ./logs:/home/node/app/logs
    environment:
      - DATABASE_URL=postgresql://postgres:postgres@db/pckd
      - DATABASE_TYPE=postgres
      - JWT_SECRET=verysecurestring
      - IPREGISTRY_API_KEY=f1ntkcjoqaazglj7
    depends_on:
      - db

  frontend:
    build:
      context: ./client
    ports:
      - 80:80
    environment:
      - BACKEND_URL=http://server:4000

  db:
    image: postgres:13-alpine
    volumes:
      - ./db:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
      - POSTGRES_DB=pckd
```

第二步：打开你的 NAS 设备

登录飞牛 NAS 或其他 NAS 设备的管理界面。

第三步：开始部署

在 NAS 中找到 Docker 应用，点击导入或创建容器。将上面的配置文件内容粘贴进去，系统会自动识别并下载所需的镜像（包括 PostgreSQL 数据库和应用镜像）。

第四步：配置环境变量

部署前需要配置以下环境变量：

必须设置：

'JWT\_SECRET'：用于 JWT 令牌签名的密钥，建议改为一个强密码（示例中的 'verysecurestring' 需要替换）

'IPREGISTRY\_API\_KEY'：用于地理位置查询的 API 密钥，可以从 [ipregistry.co](https://ipregistry.co) 免费获取（100,000 次查询/月）

可选配置：

'DATABASE\_URL'：数据库连接字符串，默认使用 PostgreSQL（如需切换到 MySQL 等其他数据库，修改此项和 'DATABASE\_TYPE'）

'BACKEND\_URL'：后端服务地址，默认为 'http://server:4000'

第五步：端口配置

前端默认使用 **80 端口**（HTTP）。如果你的 NAS 上 80 端口已被占用，可以修改配置中的这一行：

```
ports:
  - 8080:80  # 改为 8080:80 表示通过 8080 访问
```

冒号前面的数字是外部访问端口，冒号后面的是容器内部端口，只需修改前面的数字即可。

第六步：存储配置

系统会自动创建两个数据卷：

'./logs'：应用日志目录

'./db'：PostgreSQL 数据库文件目录

这些目录用于持久化存储，确保重启容器后数据不丢失。NAS 会自动管理这些目录，无需手动创建。

第七步：启动应用

勾选'开机自动启动'选项，然后点击启动。容器启动后，访问 'http://你的NAS地址' 或 'http://你的NAS地址:8080'（如果修改了端口）就能看到 Pckd 的登录界面了。

创建账户后，你就可以开始缩短 URL、自定义短链、追踪点击数据等功能了！

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/pckd-self-hosted-url-shortener-free-open-source-short-link-service-nas-one-click-deployment-fast-tracking-link-data>


## 相关文章

- [开源低代码平台Budibase快速部署指南，Docker极简搭建应用，内网穿透远程访问](open-source-low-code-platform-budibase-quick-deployment-guide-docker-simple-setup-internal-network-penetration-remote-access.md)
- [WhoDB轻量级数据库管理工具，自然语言对话查询，NAS极简部署，无公网内网穿透远程访问。](whodb-lightweight-database-management-natural-language-query-nas-simple-deployment-private-network-remote-access.md)
- [Directus 开源数据管理平台 Docker 快速部署，NAS 自建内容管理系统无公网穿透远程访问！](directus-docker-nas-cms-remote-access.md)
- [单文件PHP数据库管理工具Adminer，支持MySQL、PostgreSQL等多种数据库，NAS极简部署内网穿透远程管理](adminer-php-database-management-tool-mysql-postgresql-nas-deployment-internal-penetration-remote-management.md)
- [自然语言SQL对话工具，AI驱动数据库客户端，支持多数据库NAS快速部署](natural-language-sql-chat-ai-database-client-multi-database-nas-deployment.md)
