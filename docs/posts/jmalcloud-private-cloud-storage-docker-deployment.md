---
title: "自建私有云盘系统JmalCloud，Docker极简部署，文件安全掌控在手"
date: 2025-10-16
description: "JmalCloud私有云存储网盘系统，功能强大安全可靠。支持多种云存储后端、丰富文件预览编辑、超大文件上传断点续传、WebDAV协议等。Docker Compose一键部署，数据存储在自己服务器上，完全掌控隐私。适合家庭和企业用户快速搭建个人云盘解决方案。"
canonical: https://www.passnat.com/posts/jmalcloud-private-cloud-storage-docker-deployment
---

## 项目介绍

JmalCloud 是一款功能强大的私有云存储网盘系统，为用户提供安全可靠的个人文件管理解决方案。它基于现代化技术栈构建，集成了丰富的文件处理能力，支持多种云存储后端，让你可以完全掌控自己的数据。

这是一个真正的私有云方案，所有文件都存储在你自己的服务器上，无需担心隐私泄露。无论是家庭用户还是小型企业，都能通过简单的部署快速拥有一个功能完整的云盘系统。

项目地址：[点我跳转](https://github.com/jamebal/jmal-cloud-view)

**核心功能特性：**

✅ 支持多种云存储后端（OSS、阿里云、腾讯云、MinIO等）

✅ 丰富的文件预览能力（图片、音频、视频、Office文档、思维导图等）

✅ 强大的在线编辑功能（Word、Excel、PPT、流程图等）

✅ 超大文件上传与断点续传

✅ WebDAV 协议支持

✅ 跨平台支持（x86、arm64 等架构）

## 部署安装

想要在外网也能访问你的云盘？建议配置内网穿透方案。可以前往[帕斯内网穿透](https://passnat.com)注册账号，这样即使在公司或出门在外，也能随时随地访问家里的私有云盘。具体的操作步骤可以[参考这篇教程](https://www.passnat.com/posts/p)，帮助你快速上手。

### 使用 Docker Compose 一键部署

JmalCloud 提供了开箱即用的 Docker Compose 配置，让部署变得极其简单。

**第一步：准备配置文件**

复制下列的 docker-compose 配置内容，保存为 `docker-compose.yml` 文件：

```yaml
version: '3.8'
services:
  jmalcloud_mongodb:
    image: mongo:4.4
    container_name: jmalcloud_mongodb
    restart: always
    environment:
      MONGO_INITDB_DATABASE: jmalcloud
    volumes:
      - jmalcloud_mongodb:/data/db
    networks:
      - jmalcloud_network

  jmalcloud_server:
    image: jamebal/jmal-cloud-server:latest
    container_name: jmalcloud_server
    restart: always
    depends_on:
      - jmalcloud_mongodb
    environment:
      SPRING_DATA_MONGODB_URI: mongodb://jmalcloud_mongodb:27017/jmalcloud
    volumes:
      - jmalcloud_data:/home/jmalcloud
    networks:
      - jmalcloud_network
    ports:
      - "8080:8080"

  jmalcloud_web:
    image: jamebal/jmal-cloud-view:latest
    container_name: jmalcloud_web
    restart: always
    depends_on:
      - jmalcloud_server
    environment:
      API_URL: http://jmalcloud_server:8080
    networks:
      - jmalcloud_network
    ports:
      - "7070:80"

volumes:
  jmalcloud_mongodb:
  jmalcloud_data:

networks:
  jmalcloud_network:
    driver: bridge
```

**第二步：启动服务**

打开你的 NAS 设备或服务器的终端，进入到 `docker-compose.yml` 文件所在的目录，执行以下命令：

```bash
docker compose up -d
```

等待容器启动完成（通常需要 30-60 秒）。

**第三步：访问应用**

启动完成后，在浏览器中访问：`http://{你的设备IP}:7070`

你将看到 JmalCloud 的登录界面。使用默认账号登录：

用户名：`demo`

密码：`demo1234`

### 端口说明

7070：Web 前端访问端口（容器内为 80）

8080：后端 API 服务端口

如果你的设备上这些端口已被占用，可以修改 `docker-compose.yml` 中的端口映射。例如，将 `7070:80` 改为 `8888:80` 即可使用 8888 端口访问。

### 数据持久化

配置中已包含两个数据卷：

jmalcloud\_mongodb：存储 MongoDB 数据库文件

jmalcloud\_data：存储用户上传的文件

这些数据会自动保存在你的服务器上，即使容器重启也不会丢失。

### 环境变量配置

**必须设置的环境变量：**

无特殊必须配置项，使用默认配置即可正常运行。

**可选配置项：**

如需自定义存储路径或连接其他云存储服务（如 MinIO、阿里云 OSS 等），可在后期通过 Web 界面的管理后台进行配置。

### 重置管理员密码

如果忘记了密码，可以通过以下命令重置为默认密码 `jmalcloud`：

```bash
# 重置密码
docker exec -it jmalcloud_mongodb mongo jmalcloud --eval "db.getCollection('user').update({ 'creator': true }, {\$set: { 'password': '1000:c5b705ea13a1221f5e59110947ed806f8a978e955fbd2ed6:22508de12228c34a235454a0caf3bcaa5552858543258e56' }}, { 'multi': false, 'upsert': false })"

# 重启服务
docker restart jmalcloud_server
```

## 总结

通过以上简单的步骤，你就能在自己的服务器或 NAS 上快速部署一个功能完整的私有云盘系统。JmalCloud 让数据管理变得轻松便捷，再也不用担心文件安全问题。赶快开始体验吧！

本篇教程结束

---

原文：<https://www.passnat.com/posts/jmalcloud-private-cloud-storage-docker-deployment>
