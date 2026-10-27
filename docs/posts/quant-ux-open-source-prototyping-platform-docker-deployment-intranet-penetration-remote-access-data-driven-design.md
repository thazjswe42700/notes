---
title: "开源原型设计测试平台Quant-UX，Docker极简部署，内网穿透远程访问，数据驱动设计决策"
date: 2025-10-16
description: "Quant-UX是强大的原型设计和用户测试平台，专为设计师和产品经理打造。集原型设计、可用性测试、数据分析于一身，快速验证设计方案并获取数据驱动的用户洞察。支持交互式原型创建、定量定性数据收集，提供现代技术栈和Docker开箱即用部署方案，助力设计决策。"
canonical: https://www.passnat.com/posts/quant-ux-open-source-prototyping-platform-docker-deployment-intranet-penetration-remote-access-data-driven-design
---

## 项目介绍

Quant-UX 是一个强大的原型设计、用户测试和学习平台，专为设计师和产品经理打造。它集原型设计、可用性测试和数据分析于一身，让你能够快速验证设计方案并获得数据驱动的用户洞察。

无论你是在进行用户研究、原型测试还是收集设计反馈，Quant-UX 都能为你提供一个完整的解决方案。通过这个平台，你可以创建交互式原型、进行用户测试、收集定量和定性数据，从而做出更明智的设计决策。项目采用现代化的技术栈构建，提供了开箱即用的 Docker 部署方案，让部署变得简单快捷。

项目地址： [点我跳转](https://github.com/KlausSchaefers/quant-ux)

## 部署安装

如果你需要在公网环境下访问你的 Quant-UX 实例，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号以备后用。具体的配置步骤可以参考 [这份详细的上手指南](https://www.passnat.com/posts/p)，支持全平台操作。

### 使用 Docker Compose 快速部署

这是最推荐的部署方式，只需复制配置文件即可。

第一步：创建 docker-compose 配置文件

复制下列的配置内容到你的 `docker-compose.yml` 文件中：

```yaml
version: '3'

services:
  mongo:
    restart: always
    container_name: quant-ux-mongo
    image: mongo
    volumes:
      - ./data:/data/db

  qux-fe:
    restart: always
    container_name: quant-ux-frontend
    image: klausenschaefersinho/quant-ux
    environment:
      - QUX_PROXY_URL=http://quant-ux-backend:8080
      - QUX_AUTH=qux
      - QUX_KEYCLOAK_REALM=
      - QUX_KEYCLOAK_CLIENT=
      - QUX_KEYCLOAK_URL=
      - QUX_WS_URL=ws://127.0.0.1:8086
    links:
      - mongo
      - qux-be
    ports:
      - 8082:8082
    depends_on:
      - qux-be

  qux-be:
    restart: always
    container_name: quant-ux-backend
    image: klausenschaefersinho/quant-ux-backend
    volumes:
      - ./quant-ux-data:/app-data
    environment:
      - QUX_HTTP_HOST=http://quant-ux-frontend:8082
      - QUX_HTTP_PORT=8080
      - QUX_MONGO_DB_NAME=quantux
      - QUX_MONGO_TABLE_PREFIX=quantux
      - QUX_MONGO_CONNECTION_STRING=mongodb://quant-ux-mongo:27017
      - QUX_MAIL_USER=mail_admin@example.com
      - QUX_MAIL_PASSWORD=sTr0ngPa55w0Rd
      - QUX_MAIL_HOST=mail.example.com
      - QUX_JWT_PASSWORD=your-secure-jwt-secret-string
      - QUX_IMAGE_FOLDER_USER=/app-data/qux-images
      - QUX_IMAGE_FOLDER_APPS=/app-data/qux-image-apps
      - TZ=Asia/Shanghai
      - QUX_AUTH_SERVICE=qux
      - QUX_USER_ALLOW_SIGNUP=true
      - QUX_USER_ALLOWED_DOMAINS=*
    depends_on:
      - mongo

  qux-ws:
    restart: always
    container_name: quant-ux-websocket-server
    image: klausenschaefersinho/quant-ux-websocket
    environment:
      - QUX_SERVER=http://quant-ux-backend:8080/
      - QUX_SERVER_PORT=8086
    ports:
      - 8086:8086
    links:
      - qux-be
    depends_on:
      - qux-be
```

第二步：配置环境变量

在启动前，你需要根据实际情况修改以下关键环境变量：

必须设置：

QUX\_MAIL\_USER：你的 SMTP 邮箱地址（用于发送密码重置邮件）

QUX\_MAIL\_PASSWORD：SMTP 邮箱密码

QUX\_MAIL\_HOST：SMTP 服务器地址（如 smtp.gmail.com）

QUX\_JWT\_PASSWORD：JWT 密钥，建议设置为一个较长的随机字符串，确保系统安全性

可选设置：

TZ：时区设置，默认为 America/Chicago，可改为 Asia/Shanghai 等

QUX\_USER\_ALLOW\_SIGNUP：是否允许用户注册，默认为 true

QUX\_USER\_ALLOWED\_DOMAINS：允许注册的域名列表，\* 表示允许所有域名

⚠ 这两项合起来决定「谁能注册」，而**QUX\_USER\_ALLOW\_SIGNUP 默认就是 true**。如果你要用内网穿透把它开放到公网，建议要么把它设成 false，要么用 QUX\_USER\_ALLOWED\_DOMAINS 把可注册的邮箱域名限定成你自己的 —— 填 \* 等于不限制。

第三步：调整端口配置

如果你的主机已经占用了 8082 或 8086 端口，可以修改配置中的左侧端口号。例如，如果要将前端端口改为 8888，只需修改：

```
ports:
  - 8888:8082  # 8888 是主机端口，8082 是容器内端口
```

第四步：创建数据存储目录

在你的项目目录下创建一个 `quant-ux-data` 文件夹，用于持久化存储用户上传的图片和应用数据：

```
mkdir quant-ux-data
```

第五步：启动服务

在包含 `docker-compose.yml` 文件的目录下执行以下命令：

```
docker-compose up -d
```

等待所有容器启动完成（通常需要 1-2 分钟）。

第六步：访问应用

打开浏览器，访问 `http://你的服务器IP:8082` 即可进入 Quant-UX 应用。

如果一切正常，你会看到 Quant-UX 的登录界面。首次使用时，你可以注册一个新账户开始体验。

### 查看运行状态

如果想查看容器运行状态，可以执行：

```
docker-compose ps
```

如果想查看日志，可以执行：

```
docker-compose logs -f
```

### 停止服务

需要停止服务时，执行：

```
docker-compose down
```

## 总结

通过上述步骤，你已经成功部署了一个完整的 Quant-UX 系统，包括前端、后端、数据库和 WebSocket 服务器。现在你可以开始创建原型、进行用户测试，并收集宝贵的设计反馈数据。如有任何问题，可以参考官方文档或查看 Docker 日志进行排查。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/quant-ux-open-source-prototyping-platform-docker-deployment-intranet-penetration-remote-access-data-driven-design>


## 相关文章

- [InfluxDB时序数据库Docker部署指南，NAS内网穿透远程访问完整方案](influxdb-timeseries-docker-deployment-nas-internal-penetration-remote-access.md)
- [OpenClinica开源临床数据管理系统Docker部署指南，NAS内网穿透远程访问完整教程](openclinica-docker-deployment-nas-intranet-penetration-remote-access-guide.md)
- [开源个人数据管理框架Eidos本地部署指南，SQLite私密数据库离线使用，NAS内网穿透远程访问完整教程](eidos-open-source-personal-data-management-local-deployment-guide-sqlite-private-database-offline-usage-nas-intranet-penetration-remote-access-complete-tutorial.md)
- [开源低代码平台Budibase快速部署指南，Docker极简搭建应用，内网穿透远程访问](open-source-low-code-platform-budibase-quick-deployment-guide-docker-simple-setup-internal-network-penetration-remote-access.md)
- [开源无代码数据库Baserow自托管部署，Docker一键安装NAS内网穿透远程访问完全指南](open-source-no-code-database-baserow-self-hosted-deployment-docker-one-click-installation-nas-intranet-penetration-remote-access-complete-guide.md)
