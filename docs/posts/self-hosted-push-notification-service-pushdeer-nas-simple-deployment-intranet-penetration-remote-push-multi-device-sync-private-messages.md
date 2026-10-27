---
title: "自架推送服务平台PushDeer，NAS极简部署，支持内网穿透远程推送，多设备同步私密消息"
date: 2025-10-16
description: "PushDeer是开源自架推送服务平台，支持iOS、Mac、Android等多设备无APP推送。基于Laravel+PHP构建，提供Docker快速部署，支持文本、图片、Markdown推送，完全私有化掌控数据安全。适合NAS用户、开发者和内容创作者使用。"
canonical: https://www.passnat.com/posts/self-hosted-push-notification-service-pushdeer-nas-simple-deployment-intranet-penetration-remote-push-multi-device-sync-private-messages
---

## 项目介绍

PushDeer 是一款自架推送服务平台，为用户提供多种无 APP 推送解决方案。项目采用开源模式运营，支持 iOS、Mac、Android 等多个平台，同时还提供了轻应用、快应用以及 IoT 设备等多种接收端。无论你是 NAS 用户、开发者还是内容创作者，都能通过 PushDeer 实现便捷的消息推送功能。项目基于 Laravel + PHP 构建，支持自行架设服务器，让你完全掌控数据安全，不受任何在线服务中断的影响。

项目地址：[点我跳转](https://github.com/easychen/pushdeer)

## 部署安装

如果你需要在外网也能访问 PushDeer，建议配置内网穿透。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体操作方法可以 [点我查看](https://www.passnat.com/posts/p) 帕斯内网穿透全平台快速入门指南。

### 最快速上手配置

PushDeer 提供了开箱即用的 Docker Compose 配置，是部署的最佳选择。

**复制下列的配置文件**

```yaml
version: '2.1'
services:
  mariadb:
    image: 'mariadb:10.5.8-focal'
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "--silent","--password=$$MYSQL_ROOT_PASSWORD"]
      timeout: 10s
      retries: 3
    volumes:
      - 'mariadb_data:/var/lib/mysql'
    environment:
      - MYSQL_ROOT_PASSWORD=theVeryp@ssw0rd
      - MYSQL_DATABASE=pushdeer
    ports:
      - '3306:3306' 
  redis: 
    image: 'bitnami/redis:6.0.16'
    healthcheck:
      test: ["CMD", "redis-cli","ping"]
    environment:
      - ALLOW_EMPTY_PASSWORD=yes 
  app:
    build: './docker/web/'
    ports:
      - '8800:80'
    volumes:
      - './:/app'
    depends_on:
      mariadb:
        condition: service_healthy
      redis:
        condition: service_healthy
    environment:
      - DB_HOST=mariadb
      - DB_PORT=3306
      - DB_USERNAME=root
      - DB_DATABASE=pushdeer
      - DB_PASSWORD=theVeryp@ssw0rd
volumes:
  mariadb_data:
```

**打开飞牛 NAS 或其他的 NAS 设备**

点击本地镜像 - 添加镜像 - 从 URL 添加，这一步先添加 docker 镜像。

镜像填 webdevops/php-apache:8.0-alpine，等待下载完成后点击启动按钮启动容器。

**开始配置**

勾选开机自动启动后下一步。端口设置这里需要特别注意，容器内部使用 80 端口进行 Web 服务，建议映射到宿主机的 8800 端口（如果 8800 端口被占用，可以修改为其他未使用的端口，但容器内的 80 端口保持不变）。同时数据库服务需要映射 3306 端口用于 MySQL 连接。

存储位置这里，需要将项目目录映射进容器。建议在 NAS 上创建一个专用文件夹（比如 /mnt/pushdeer），将其映射到容器内的 /app 目录，这样可以持久化保存所有配置和数据。

**环境变量设置**

必须设置：

DB\_HOST: mariadb（数据库主机）

DB\_PORT: 3306（数据库端口）

DB\_USERNAME: root（数据库用户名）

DB\_DATABASE: pushdeer（数据库名称）

DB\_PASSWORD: theVeryp@ssw0rd（数据库密码，强烈建议修改此默认密码为安全的密码）

选择设置，可以不填：

APP\_DEBUG: false（调试模式，生产环境保持 false）

其他的都不用填写，直接下一步启动就好了。

**访问应用**

创建成功后可以访问 http://你的NAS地址:8800 来打开 PushDeer 管理界面。看到登录页面表示部署成功。

首次使用需要通过 Apple 账号或微信账号登录（Android 版支持微信），然后在「设备」标签页注册你的设备，在「Key」标签页生成推送密钥即可开始使用。

**部署完成！**

现在你已经拥有了一个完全私有的推送服务平台。可以通过生成的 Key 和简单的 URL 调用实现文本推送、图片推送、Markdown 推送等多种功能。支持在多台设备间同步消息，完全掌控你的推送数据。

---

原文：<https://www.passnat.com/posts/self-hosted-push-notification-service-pushdeer-nas-simple-deployment-intranet-penetration-remote-push-multi-device-sync-private-messages>


## 相关文章

- [Standard Notes 私密笔记自建 - 端到端加密开源方案，NAS 极简部署指南](standard-notes-private-notes-self-hosted-end-to-end-encryption-open-source-nas-simple-deployment-guide.md)
- [免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库](lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access.md)
- [自建音乐流媒体服务器Polaris，免费开源支持多格式，NAS一键部署内网穿透远程听歌](polaris-open-source-music-server-nas-remote-streaming.md)
- [WeekToDo开源周计划管理应用，极简任务管理，本地部署隐私保护，Docker一键启动NAS自建。](week-todo-open-source-task-management-local-deployment-docker-nas.md)
- [私有部署开源聊天应用VoceChat，NAS轻松搭建安全私密聊天系统，内网穿透远程访问](vocechat-private-deployment-nas-internal-network-penetration.md)
