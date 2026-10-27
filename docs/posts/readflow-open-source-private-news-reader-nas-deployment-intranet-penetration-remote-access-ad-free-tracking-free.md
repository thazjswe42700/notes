---
title: "开源私密新闻阅读器Readflow，NAS极简部署，内网穿透远程访问，无广告无追踪"
date: 2025-10-16
description: "Readflow是开源无广告的个人新闻阅读器，支持RSS、Pocket、Wallabag等多源聚合。采用Go开发，提供简洁高效的后端与现代化前端界面，电脑手机均可使用。支持文章分类、离线保存、导出HTML/PDF/EPUB等多种格式，提供完整隐私保护与自由体验。"
canonical: https://www.passnat.com/posts/readflow-open-source-private-news-reader-nas-deployment-intranet-penetration-remote-access-ad-free-tracking-free
---

## 项目介绍

Readflow 是一款功能强大的个人新闻阅读器，让你可以在一个统一的平台上管理和阅读来自互联网各处的文章内容。它完全开源、无广告、无追踪器，为用户提供了真正的隐私保护和自由体验。

项目采用 Go 语言开发，提供了简洁高效的后端服务，配合现代化的前端界面，无论是在电脑还是手机上都能获得一致的优秀体验。你可以从各种来源导入文章、创建分类、离线保存，甚至可以将内容导出为 HTML、PDF、EPUB 等多种格式。项目还支持与 RSS、Pocket、Wallabag 等多个外部服务进行集成，通过 Webhook 实现强大的自动化功能。

项目地址：[点我跳转](https://github.com/ncarliel/readflow)

## 部署安装

如需在外网也能访问你的 Readflow 实例，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) 来实现远程访问，先注册账号备用。具体的配置步骤可以参考 [这份详细教程](https://www.passnat.com/posts/p)，帮助你快速上手全平台的内网穿透方案。

### 使用 Docker Compose 快速启动

这是最推荐的方式，因为 Readflow 需要配合 PostgreSQL 数据库使用。

复制下列的配置文件，保存为 `docker-compose.yml`：

```yaml
services:
  ########################################
  # PostgreSQL 数据库
  ########################################
  db:
    image: postgres:17
    restart: always
    environment:
      - POSTGRES_DB=readflow
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=your_password_here
    volumes:
      - db-data:/var/lib/postgresql/data

  ########################################
  # Readflow 应用
  ########################################
  readflow:
    image: 'ncarlier/readflow:edge'
    restart: always
    depends_on:
      - db
    ports:
      - '8080:8080'
    environment:
      - READFLOW_DATABASE_URI=postgres://postgres:your_password_here@db/readflow?sslmode=disable
      - READFLOW_AUTHN_METHOD=basic
      - READFLOW_AUTHN_BASIC_HTPASSWD_FILE=file:///var/local/demo.htpasswd
    volumes:
      - ./demo.htpasswd:/var/local/demo.htpasswd

volumes:
  db-data:
```

打开你的飞牛 NAS 或其他 NAS 设备，进入 Docker 管理界面。

首先需要创建认证文件。在你的主机上创建一个 `demo.htpasswd` 文件，内容如下（用户名：demo，密码：demo）：

```
demo:$apr1$r31....
```

或者使用以下命令生成：

```
echo 'demo:demo' | htpasswd -c demo.htpasswd demo
```

然后将上面的 `docker-compose.yml` 文件上传到 NAS，进入该文件所在的目录，执行：

```bash
docker compose up -d
```

**重要提示：** 请务必修改 `POSTGRES\_PASSWORD` 的值为你自己的强密码，同时更新 `READFLOW\_DATABASE\_URI` 中对应的密码。

### 端口配置

容器内部端口：8080（Readflow Web 服务）

映射到主机的端口：可以根据需要修改，如果 8080 端口已被占用，可以改为其他端口，例如 `'8081:8080'`

### 存储位置

数据库数据：存储在 `db-data` 卷中，用于持久化 PostgreSQL 数据库

用户认证文件：需要在本地创建 `demo.htpasswd` 文件并挂载到容器中

### 环境变量设置

**必须设置：**

`POSTGRES\_PASSWORD`：PostgreSQL 数据库密码（请设置为强密码）

`READFLOW\_DATABASE\_URI`：数据库连接字符串（格式：`postgres://用户名:密码@db/数据库名?sslmode=disable`）

**可选设置：**

`READFLOW\_AUTHN\_METHOD`：认证方式（默认为 basic，即基本认证）

`PORT`：如需修改容器映射的端口，可以在 docker-compose.yml 中调整

其他配置项保持默认即可，无需额外设置。

### 启动应用

配置完成后，执行启动命令：

```bash
docker compose up -d
```

等待容器启动完成，即可通过浏览器访问 `http://你的NAS地址:8080` 来打开 Readflow。

使用默认账号登录：

用户名：demo

密码：demo

首次登录后，建议立即修改密码以保护你的账户安全。

## 总结

通过上述步骤，你已经成功在 NAS 上部署了 Readflow 个人新闻阅读器。现在你可以开始添加各种新闻源、创建分类、离线保存文章，享受一个整洁、私密的阅读体验。如有任何问题，可以参考官方文档：https://docs.readflow.app

本篇教程结束

---

原文：<https://www.passnat.com/posts/readflow-open-source-private-news-reader-nas-deployment-intranet-penetration-remote-access-ad-free-tracking-free>


## 相关文章

- [Stump 电子书库：NAS Docker 部署 + 内网穿透](stump-nas-docker-ebook-library-reading-progress-intranet-penetration.md)
- [魔豆文库MOREDOC开源部署指南，Golang企业级文档管理系统，支持多格式文件预览，NAS内网穿透轻松搭建](moredoc-golang-enterprise-document-management-system-multi-format-preview-nas-intranet.md)
- [轻量级自托管仪表板Glance，极简部署NAS内网穿透远程访问信息聚合平台](glance-lightweight-self-hosted-dashboard-nas-intranet-penetration-remote-access-information-aggregation-platform.md)
- [自建 RSS 阅读器 FreshRSS：飞牛 NAS Docker 部署，关注的更新汇到一页，配内网穿透在外面也能刷](self-hosted-rss-reader-freshrss-nas-docker-deployment-intranet-penetration.md)
- [开箱即用网络文学聚合阅读平台 Reader，多源搜索云端同步，NAS部署无需公网远程阅读](reader-network-literature-aggregation-platform-multi-source-search-cloud-sync-nas-deployment.md)
