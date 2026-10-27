---
title: "开源低代码平台Budibase快速部署指南，Docker极简搭建应用，内网穿透远程访问"
date: 2025-10-16
description: "Budibase是开源低代码平台，快速构建表单、门户和审批应用。支持MongoDB、PostgreSQL、MySQL等多数据源，拖拽组件设计，响应式布局，工作流自动化，企业级部署。节省数百小时开发时间，完全开源可控，适合企业和开发者快速原型和生产应用开发。"
canonical: https://www.passnat.com/posts/open-source-low-code-platform-budibase-quick-deployment-guide-docker-simple-setup-internal-network-penetration-remote-access
---

## 项目介绍

Budibase 是一个开源的低代码平台，旨在让工程师快速构建表单、门户和审批应用程序。它基于现代技术栈构建，支持多数据源连接、响应式设计、工作流自动化和企业级部署。无论你是要快速原型设计还是构建生产级应用，Budibase 都能帮你节省数百小时的开发时间。

这个平台最大的特点是开箱即用，你可以通过拖拽组件快速搭建应用，支持连接 MongoDB、PostgreSQL、MySQL、Airtable、S3 等多种数据源。界面设计精美，功能强大，完全开源，让你有完全的控制权和扩展性。无论是中小企业还是个人开发者，都能轻松上手。

项目地址：[点我跳转](https://github.com/Budibase/budibase)

## 部署安装

如果你需要在外网也能访问 Budibase，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### 使用 Docker Compose 快速部署（推荐）

这是最简单的部署方式，适合所有用户。

**第一步：准备配置文件**

复制下列的 docker-compose 配置文件到你的服务器上（创建一个文件夹，比如 `budibase`）：

```yaml
version: '3'
services:
  couchdb:
    image: couchdb:3.2
    environment:
      - COUCHDB_PASSWORD=budibase
      - COUCHDB_USER=budibase
    volumes:
      - couchdb_data:/opt/couchdb/data
    ports:
      - "5984:5984"

  minio:
    image: minio/minio:latest
    environment:
      - MINIO_ROOT_USER=minioadmin
      - MINIO_ROOT_PASSWORD=minioadmin
    volumes:
      - minio_data:/data
    ports:
      - "9000:9000"
      - "9001:9001"
    command: server /data --console-address ":9001"

  budibase:
    image: budibase/budibase:latest
    environment:
      - COUCHDB_URL=http://budibase:budibase@couchdb:5984
      - MINIO_URL=http://minio:9000
      - MINIO_ACCESS_KEY=minioadmin
      - MINIO_SECRET_KEY=minioadmin
      - JWT_SECRET=your-secret-key-change-this
      - INTERNAL_API_KEY=your-internal-key-change-this
    ports:
      - "10000:10000"
    depends_on:
      - couchdb
      - minio

volumes:
  couchdb_data:
  minio_data:
```

**第二步：启动服务**

打开你的 NAS 设备或服务器终端，进入到配置文件所在的目录，执行以下命令：

```
docker-compose up -d
```

这个命令会在后台启动所有必要的服务。首次启动会下载镜像，可能需要几分钟时间，请耐心等待。

**第三步：配置说明**

上面的配置文件中有几个重要的地方需要你注意：

JWT\_SECRET 和 INTERNAL\_API\_KEY：这两个是安全密钥，一定要改成复杂的随机字符串，不要使用默认值

COUCHDB\_PASSWORD 和 MINIO\_ROOT\_PASSWORD：数据库和对象存储的密码，建议修改为强密码

端口映射：

10000 是 Budibase 主应用的访问端口

5984 是 CouchDB 数据库端口

9000/9001 是 MinIO 对象存储端口

如果这些端口在你的系统上已被占用，可以修改冒号前面的数字。比如想用 8080 端口访问 Budibase，就改成 8080:10000。

**第四步：访问应用**

等待所有容器启动完成后（通常需要 1-2 分钟），打开浏览器访问：

```
http://你的服务器IP:10000
```

如果是本地部署，直接访问：

```
http://localhost:10000
```

就能看到 Budibase 的登录界面了。首次访问会引导你创建管理员账号，按照提示完成即可。

**第五步：开机自启（可选）**

如果你想让 Budibase 在服务器重启后自动启动，可以创建一个 systemd 服务文件或在 NAS 的计划任务中添加启动命令。

## 总结

通过上面的步骤，你已经成功部署了一个完整的 Budibase 低代码平台。现在你可以开始创建各种业务应用了。如果遇到任何问题，可以查看 [官方文档](https://docs.budibase.com/docs) 或在 [GitHub 社区](https://github.com/Budibase/budibase/discussions) 寻求帮助。

祝你使用愉快！

---

原文：<https://www.passnat.com/posts/open-source-low-code-platform-budibase-quick-deployment-guide-docker-simple-setup-internal-network-penetration-remote-access>
