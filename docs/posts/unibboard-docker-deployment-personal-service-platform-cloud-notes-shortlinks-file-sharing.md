---
title: "个人综合服务平台UniBoard Docker极简部署，云笔记短链接文件分享一站式解决方案"
date: 2025-10-16
description: "UniBoard是集个人主页、导航页、云笔记、短链接和文件分享于一身的综合服务平台。采用Vue3和Kotlin技术构建，支持自定义编辑、Markdown笔记、短链接生成、文件分享和图床服务。提供暗色模式，可用Docker快速部署，适合个人品牌展示和日常工具使用。"
canonical: https://www.passnat.com/posts/unibboard-docker-deployment-personal-service-platform-cloud-notes-shortlinks-file-sharing
---

## 项目介绍

UniBoard 是一个功能强大的个人综合服务平台，集个人主页、导航页、云笔记、短链接和文件分享于一身。该项目采用前后端分离的架构，前端使用 Vue 3 + TypeScript + Primevue + Tailwind CSS 构建，后端基于 Kotlin 开发，提供了完整的 RESTful API 支持。

UniBoard 不仅提供了优雅的用户界面，还具备丰富的功能特性：支持个人信息和横幅的完全自定义编辑、Markdown 云笔记存储、短链接生成与统计、文件存储与分享、内置图床服务等。此外，项目还支持全局暗色模式，让你可以随时随地管理个人信息、笔记、资源和链接。无论是作为个人品牌展示还是日常工具使用，UniBoard 都能满足你的需求。

项目地址：[点我跳转](https://github.com/Coooolfan/UniBoard)

## 部署安装

如果你需要在外网访问这个项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地访问你的 UniBoard 平台。具体的操作指南可以 [点我查看](https://www.passnat.com/posts/p)。

UniBoard 提供了开箱即用的 Docker Compose 部署方案，非常适合初学者快速上手。

### Docker Compose 快速部署

**第一步：下载配置文件**

打开你的 NAS 设备或任何支持 Docker 的服务器，执行以下命令下载所需的配置文件：

```
mkdir uniboard
cd uniboard
wget https://github.com/Coooolfan/UniBoard/releases/latest/download/docker-compose.yml
wget https://github.com/Coooolfan/UniBoard/releases/latest/download/example.env
```

**第二步：修改配置文件**

下载完成后，你需要修改配置文件以适应你的环境：

首先将 'example.env' 复制为 '.env' 文件：

```
cp example.env .env
```

用文本编辑器打开 '.env' 文件，根据提示修改相关参数。**最重要的是修改 'POSTGRES\_PASSWORD' 字段，千万不要使用默认值！** 这是数据库的密码，关系到数据安全。

打开 'docker-compose.yml' 文件，检查第 6 行的端口配置。默认情况下，UniBoard 会通过 '8888' 端口向外暴露所有服务。如果你的 '8888' 端口已被占用，可以修改这个端口号，但需要保证格式正确。

**第三步：启动服务**

配置完成后，使用 Docker Compose 启动服务：

```bash
docker compose up -d
```

稍等片刻，服务会在后台启动。

**第四步：访问应用**

启动完成后，在浏览器中访问 'http://localhost:8888'（如果你修改了端口，请使用对应的端口号）。如果是在远程服务器上部署，将 'localhost' 替换为你的服务器 IP 地址即可。

### 环境变量说明

在 '.env' 文件中，以下是重要的环境变量配置：

**必须设置：**

'POSTGRES\_PASSWORD'：数据库密码，请务必修改为强密码

**可选设置：**

根据你的实际需求，可以调整其他参数。具体的环境变量说明请参考 'example.env' 文件中的注释。

### 使用 Nginx 反向代理（可选但推荐）

如果你想通过域名访问 UniBoard，或者需要配置 HTTPS，建议使用 Nginx 进行反向代理。这样可以提高安全性和访问体验。

在你的 Nginx 配置文件中添加以下配置片段：

```
client_max_body_size 0;
location / {
    proxy_pass http://127.0.0.1:8888;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_redirect default;
    proxy_buffering off;
}
```

配置完成后，重新加载 Nginx：

```
nginx -s reload
```

### 升级更新

如果你想升级到最新版本，只需执行以下命令：

```bash
docker compose pull && docker compose up -d
```

系统会自动拉取最新镜像并重启服务。

**注意**：0.3.0 版本与之前的版本完全不兼容，升级前请备份重要数据。

通过以上步骤，你就能成功部署 UniBoard，拥有一个属于自己的个人综合服务平台。无论是管理笔记、分享文件还是展示个人品牌，UniBoard 都能为你提供便捷的解决方案。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/unibboard-docker-deployment-personal-service-platform-cloud-notes-shortlinks-file-sharing>
