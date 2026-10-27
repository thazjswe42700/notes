---
title: "无代码网页数据提取Maxun自建指南，2分钟训练机器人自动爬取，内网穿透远程访问"
date: 2025-10-16
description: "Maxun是一个强大的无代码网页数据提取平台，2分钟内训练机器人自动爬取网页数据。基于TypeScript构建，提供直观可视化界面，支持列表捕获、文本提取、截图等功能。一键部署Docker Compose，支持定时运行、网站转API、登录后数据提取等高级功能，让数据提取变得简单高效，无需任何编程基础。"
canonical: https://www.passnat.com/posts/no-code-web-scraping-maxun-guide-2-minute-robot-training-intranet-penetration-remote-access
---

Maxun - 无代码网页数据提取平台自建指南

## 项目介绍

Maxun 是一个强大的无代码网页数据提取平台，让你在 2 分钟内训练一个机器人自动爬取网页数据。它基于 TypeScript 构建，提供了一个直观的可视化界面，无需编写任何代码就能轻松实现网页数据自动化提取。

无论是进行市场调研、潜在客户挖掘、内容聚合还是其他数据采集需求，Maxun 都能帮你轻松搞定。你可以创建自定义机器人来模拟用户操作，支持列表捕获、文本提取、截图等多种操作方式。最强大的是，一旦机器人创建完成，它就会持续自动为你工作，无需任何人工干预。

项目还支持定时运行、网站转 API、网站转电子表格、自适应网站布局变化、登录后数据提取等高级功能，让数据提取变得前所未有的简单。

项目地址：[点我跳转](https://github.com/getmaxun/maxun)

## 部署安装

如需在外网也能打开项目需要配置内网穿透使用，点击前往 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的操作方法可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略。

### Docker Compose 部署（推荐）

Maxun 使用 Docker Compose 部署最为简便，只需几个步骤即可快速启动。

**第一步：准备配置文件**

在你的 NAS 或服务器上创建一个项目文件夹，例如 `maxun`。在该文件夹中创建 `.env` 文件，用于存储环境变量配置。

**第二步：创建 docker-compose.yml**

复制下列的配置文件到你的项目根目录：

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: ${DB_NAME}
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    ports:
      - "${DB_PORT}:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - maxun-network

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    networks:
      - maxun-network

  minio:
    image: minio/minio
    environment:
      MINIO_ROOT_USER: ${MINIO_ACCESS_KEY}
      MINIO_ROOT_PASSWORD: ${MINIO_ACCESS_KEY}
    ports:
      - "${MINIO_PORT}:9000"
      - "${MINIO_CONSOLE_PORT}:9001"
    volumes:
      - minio_data:/data
    command: server /data --console-address ":9001"
    networks:
      - maxun-network

  backend:
    image: getmaxun/maxun-backend:latest
    environment:
      BACKEND_PORT: ${BACKEND_PORT}
      BACKEND_URL: ${BACKEND_URL}
      VITE_BACKEND_URL: ${VITE_BACKEND_URL}
      JWT_SECRET: ${JWT_SECRET}
      DB_NAME: ${DB_NAME}
      DB_USER: ${DB_USER}
      DB_PASSWORD: ${DB_PASSWORD}
      DB_HOST: postgres
      DB_PORT: 5432
      ENCRYPTION_KEY: ${ENCRYPTION_KEY}
      SESSION_SECRET: ${SESSION_SECRET}
      MINIO_ENDPOINT: minio
      MINIO_PORT: ${MINIO_PORT}
      MINIO_ACCESS_KEY: ${MINIO_ACCESS_KEY}
    ports:
      - "${BACKEND_PORT}:${BACKEND_PORT}"
    depends_on:
      - postgres
      - redis
      - minio
    networks:
      - maxun-network

  frontend:
    image: getmaxun/maxun-frontend:latest
    environment:
      FRONTEND_PORT: ${FRONTEND_PORT}
      PUBLIC_URL: ${PUBLIC_URL}
      VITE_PUBLIC_URL: ${VITE_PUBLIC_URL}
      VITE_BACKEND_URL: ${VITE_BACKEND_URL}
    ports:
      - "${FRONTEND_PORT}:${FRONTEND_PORT}"
    depends_on:
      - backend
    networks:
      - maxun-network

volumes:
  postgres_data:
  minio_data:

networks:
  maxun-network:
    driver: bridge
```

**第三步：配置环境变量**

打开飞牛 NAS 或其他 NAS 设备，在项目根目录创建 `.env` 文件，并填入以下配置：

**环境变量设置：必须设置**

```
BACKEND_PORT=8080
FRONTEND_PORT=5173
BACKEND_URL=http://localhost:8080
VITE_BACKEND_URL=http://localhost:8080
PUBLIC_URL=http://localhost:5173
VITE_PUBLIC_URL=http://localhost:5173
JWT_SECRET=your-super-secret-jwt-key-change-this
DB_NAME=maxun
DB_USER=maxun_user
DB_PASSWORD=your-secure-password-here
DB_HOST=postgres
DB_PORT=5432
ENCRYPTION_KEY=your-encryption-key-32-chars-long
MINIO_ENDPOINT=minio
MINIO_PORT=9000
MINIO_ACCESS_KEY=minioadmin
```

**环境变量设置：选择设置（可选）**

```
SESSION_SECRET=your-session-secret-key
MINIO_CONSOLE_PORT=9001
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret
GOOGLE_REDIRECT_URI=http://localhost:5173/auth/google/callback
AIRTABLE_CLIENT_ID=your-airtable-client-id
AIRTABLE_REDIRECT_URI=http://localhost:5173/auth/airtable/callback
MAXUN_TELEMETRY=true
```

**重要提示：**

JWT\_SECRET 和 ENCRYPTION\_KEY 必须设置为强随机字符串，用于数据加密和认证

DB\_PASSWORD 请修改为你自己的安全密码

MINIO\_ACCESS\_KEY 是 MinIO 存储服务的访问密钥，用于存储机器人运行时的截图

如果端口 8080 或 5173 被占用，可以修改 BACKEND\_PORT 和 FRONTEND\_PORT 的值，但容器内部端口保持不变

**第四步：启动服务**

在项目根目录打开终端，执行以下命令启动所有服务：

```
docker-compose up -d
```

等待所有容器启动完成（通常需要 1-2 分钟）。

**第五步：访问应用**

启动完成后，你可以通过以下地址访问 Maxun：

**前端界面**：http://localhost:5173/

**后端 API**：http://localhost:8080/

**MinIO 管理面板**：http://localhost:9001/

使用浏览器打开前端地址，即可开始创建你的第一个数据提取机器人。

## 功能特性

✨ **无代码数据提取** - 通过可视化界面轻松创建数据提取规则

✨ **智能分页处理** - 自动处理网页分页和滚动加载

✨ **定时任务** - 设置机器人按照指定时间表自动运行

✨ **网站转 API** - 将任何网站转换为可调用的 API 接口

✨ **网站转电子表格** - 直接将提取的数据导出到 Google Sheets 或 Airtable

✨ **自适应布局** - 自动适应网站布局变化，无需重新配置

✨ **登录支持** - 支持提取需要登录才能访问的数据

✨ **集成能力** - 支持 Google Sheets、Airtable 等第三方服务集成

## 部署完成

到这里，Maxun 就已经成功部署在你的 NAS 或服务器上了。你现在可以：

访问 http://localhost:5173 打开 Maxun 的可视化界面

创建你的第一个机器人，选择要提取的网站

通过拖拽和点击配置数据提取规则

运行机器人自动提取数据

将数据导出到 API、电子表格或其他目标

Maxun 的强大之处在于它完全无需编码，即使是技术小白也能轻松上手。无论你是做市场研究、竞争对手分析、价格监控还是内容聚合，Maxun 都能帮你节省大量的时间和人力成本。

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/no-code-web-scraping-maxun-guide-2-minute-robot-training-intranet-penetration-remote-access>


## 相关文章

- [开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问](appsmith-nas-docker-compose-local-deployment-guide.md)
- [本地密码生成器PSWD，隐私优先TypeScript构建，支持Docker和NAS快速部署，内网穿透远程访问](local-password-generator-pswd-privacy-first-typescript-docker-nas-deployment-intranet-penetration-remote-access.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [开源无代码数据库SeaTable自托管部署指南，NAS极简配置Docker Compose一键启动](seatable-self-hosted-nas-docker-compose-deployment-guide.md)
- [开源问卷调查系统LimeSurvey，NAS一键部署，内网穿透远程访问，无限问卷模板](open-source-survey-system-limesurvey-nas-one-click-deployment-intranet-penetration-remote-access-unlimited-questionnaire-templates.md)
