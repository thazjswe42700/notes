---
title: "Directus 开源数据管理平台 Docker 快速部署，NAS 自建内容管理系统无公网穿透远程访问！"
date: 2025-10-16
description: "Directus是开源实时API和数据管理平台，支持PostgreSQL、MySQL、SQLite等多种SQL数据库。提供REST和GraphQL API、无代码仪表板、灵活部署和细粒度权限控制。适合构建Headless CMS、内部工具和后端服务，支持Docker快速部署。"
canonical: https://www.passnat.com/posts/directus-docker-nas-cms-remote-access
---

Directus 是一个实时 API 和应用仪表板，用于管理 SQL 数据库内容。它是一个功能强大的开源后端即服务（BaaS）平台，为开发者和非技术用户提供了一个统一的数据管理解决方案。

Directus 基于 Node.js 构建，支持 REST 和 GraphQL API，可以瞬间为任何 SQL 数据库提供一个高性能的 API 层。无论你是使用 PostgreSQL、MySQL、SQLite、OracleDB、CockroachDB、MariaDB 还是 MS-SQL，Directus 都能完美适配。

项目的核心特性包括：

即插即用的 API：无需迁移现有数据库，直接为你的 SQL 数据库生成 REST 和 GraphQL API

现代化的无代码仪表板：基于 Vue.js 构建的直观界面，即使非技术用户也能轻松管理数据

灵活的部署方式：支持本地部署、自托管或使用官方云服务

完全可扩展：模块化架构设计，易于定制和白标签化

强大的访问控制：细粒度的权限管理，确保数据安全

无论你是在构建 Headless CMS、内部工具、数据分析平台还是后端服务，Directus 都能为你提供一个强大而灵活的基础。

项目地址：[点我跳转](https://github.com/directus/directus)

## 部署安装

如需在外也打开项目需要配置内网穿透使用，点击前往 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的操作方法可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略

### 使用 Docker Compose 快速部署

复制下列的配置文件，保存为 'docker-compose.yml'：

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_PASSWORD: directus_password
      POSTGRES_DB: directus
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  directus:
    image: directus/directus:latest
    ports:
      - "8055:8055"
    environment:
      KEY: your_secret_key_here
      SECRET: your_secret_here
      DB_CLIENT: postgres
      DB_HOST: postgres
      DB_PORT: 5432
      DB_DATABASE: directus
      DB_USER: postgres
      DB_PASSWORD: directus_password
      ADMIN_EMAIL: admin@example.com
      ADMIN_PASSWORD: admin_password
      PUBLIC_URL: http://localhost:8055
    depends_on:
      - postgres
    volumes:
      - ./uploads:/directus/uploads
      - ./extensions:/directus/extensions

volumes:
  postgres_data:
```

打开飞牛 NAS 或其他的 NAS 设备，进入 Docker 管理界面。

**第一步：创建数据库卷**

在 NAS 上创建一个文件夹用于存储 PostgreSQL 数据，例如 '/mnt/data/directus/postgres'。

**第二步：创建上传文件夹**

创建用于存储上传文件的目录：'/mnt/data/directus/uploads' 和 '/mnt/data/directus/extensions'。

**第三步：导入 Docker Compose 配置**

将上面的配置文件复制到你的 NAS 中，然后在 Docker 管理界面中选择'从文件导入'或'从 URL 导入'。

**第四步：配置环境变量**

环境变量设置说明：

**必须设置：**

'KEY'：应用加密密钥，用于加密敏感数据。建议使用随机生成的长字符串

'SECRET'：应用密钥，用于签名 JWT 令牌。同样建议使用随机字符串

'ADMIN\_EMAIL'：管理员邮箱地址

'ADMIN\_PASSWORD'：管理员初始密码

'DB\_PASSWORD'：PostgreSQL 数据库密码

**可选设置：**

'PUBLIC\_URL'：应用的公开访问地址，用于生成正确的链接

'CORS\_ENABLED'：是否启用 CORS，默认为 true

'CORS\_ORIGIN'：允许的 CORS 源地址

其他变量保持默认即可，直接下一步启动容器。

**第五步：端口配置**

Directus 应用访问端口：'8055'（容器内部端口，如果 NAS 上 8055 被占用，可修改左侧的映射端口，例如改为 '8056:8055'）

PostgreSQL 数据库端口：'5432'（如无特殊需求，建议保持不变）

如果提示端口被占用，只需修改冒号前面的端口号即可，容器内的端口保持不变。

**第六步：启动容器**

点击启动按钮，等待容器启动完成。首次启动会自动初始化数据库和创建管理员账户。

创建成功后可以访问 'http://你的NAS地址:8055' 来打开 Directus 管理界面。

使用你设置的管理员邮箱和密码登录，即可开始使用 Directus 管理你的数据库内容。

到这里就可以正常显示项目了。

### 使用 Docker 单命令部署（简化版）

如果你只想快速体验 Directus，可以使用以下单命令（需要提前安装 PostgreSQL）：

```bash
docker run -d \
  -p 8055:8055 \
  -e KEY=your_secret_key \
  -e SECRET=your_secret \
  -e DB_CLIENT=postgres \
  -e DB_HOST=your_postgres_host \
  -e DB_PORT=5432 \
  -e DB_DATABASE=directus \
  -e DB_USER=postgres \
  -e DB_PASSWORD=your_db_password \
  -e ADMIN_EMAIL=admin@example.com \
  -e ADMIN_PASSWORD=admin_password \
  -v /path/to/uploads:/directus/uploads \
  directus/directus:latest
```

将其中的参数替换为你的实际配置即可。

## 总结

Directus 是一个功能完整、易于部署的数据管理平台。通过 Docker Compose，你可以在几分钟内搭建起一个完整的后端系统，无需复杂的配置。无论是用于内容管理、数据分析还是 API 服务，Directus 都能提供强大的支持。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/directus-docker-nas-cms-remote-access>
