---
title: "开源日程管理系统Cal.com自托管部署指南，NAS极简配置内网穿透远程访问"
date: 2025-10-16
description: "Cal.com是开源日程管理预约平台，Calendly完美替代品。基于Next.js、React、Tailwind CSS构建，支持自托管、白标部署、自定义域名。完全掌控数据、工作流程和设计。适用个人日程、商务会议、课程排课、家庭通话。企业级功能灵活性强，提供API驱动支持。"
canonical: https://www.passnat.com/posts/open-source-calendar-management-system-cal-com-self-hosted-deployment-guide-nas-simple-configuration-intranet-penetration-remote-access
---

## 项目介绍

Cal.com 是一个开源的日程管理和预约调度平台，被誉为 Calendly 的开源替代品。它基于 Next.js、React、Tailwind CSS 和 Prisma 构建，提供了完整的自托管解决方案。无论是个人日程管理、商务会议预约、瑜伽课程排课，还是家庭通话安排，Cal.com 都能胜任。

与商业化的日程工具不同，Cal.com 让你完全掌控自己的数据、工作流程和外观设计。支持白标部署、API 驱动、自定义域名，让你拥有真正属于自己的日程管理系统。无论是自托管还是由官方托管，你都能获得企业级的功能和灵活性。

项目地址：[点我跳转](https://github.com/calcom/cal.com)

## 部署安装

如果你想在办公室外也能访问 Cal.com，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的跨平台部署攻略可以 [点我查看](https://www.passnat.com/posts/p)，轻松实现远程访问。

### 使用 Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板

点击'本地镜像'→'添加镜像'→'从 URL 添加'

在镜像地址栏填入：calcom/cal.com:latest

等待镜像下载完成后，点击启动按钮启动容器

### 端口配置

容器内部端口：**3000**（这是 Cal.com 的默认访问端口）

主机映射端口：建议设置为 **3000**，如果该端口已被占用，可修改为其他端口（如 **3001**、**3002** 等），但容器内部端口保持 **3000** 不变

### 存储配置

Cal.com 需要持久化数据存储，建议在 NAS 上创建以下文件夹并进行映射：

/data - 用于存储数据库和应用数据

在 NAS 管理面板中创建一个名为 **cal.com** 的文件夹，然后将其映射到容器的 **/data** 目录

### 环境变量设置

必须设置：

DATABASE\_URL - PostgreSQL 数据库连接字符串（格式：**postgresql://user:password@host:port/database**）

NEXTAUTH\_SECRET - 用于 NextAuth 认证的密钥（可用 **openssl rand -base64 32** 生成）

CALENDSO\_ENCRYPTION\_KEY - 数据加密密钥（可用 **openssl rand -base64 32** 生成）

可选设置：

NEXTAUTH\_URL - 应用的外部访问 URL（默认：**http://localhost:3000**）

NEXT\_PUBLIC\_LOGGER\_LEVEL - 日志级别（0-6，默认：3）

如果你没有 PostgreSQL 数据库，可以使用 Railway.app 或 Render 等服务快速创建云数据库。

勾选'开机自动启动'确保容器在 NAS 重启后自动启动

点击下一步，完成容器启动

### 访问应用

容器启动成功后，在浏览器中访问 **http://你的NAS地址:3000**（如果修改了端口号，请使用对应的端口）

使用默认账号登录，即可开始使用 Cal.com 进行日程管理和预约调度

到这里，Cal.com 就已经成功部署并可以正常使用了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-calendar-management-system-cal-com-self-hosted-deployment-guide-nas-simple-configuration-intranet-penetration-remote-access>


## 相关文章

- [开源日程管理系统Cal.com自建部署，完全控制数据NAS本地托管，支持内网穿透远程访问](open-source-calendar-management-cal-com-self-hosted-deployment-local-nas-storage-intranet-penetration-remote-access.md)
- [NotNav导航网站Notion数据库驱动，Next.js打造响应式设计支持内外网切换](notnav-notion-database-nextjs-responsive-design.md)
- [自建云相册Lsky Pro，NAS极简部署，支持多云存储，内网穿透远程访问图片管理分享系统](lsky-pro-nas-deployment-multi-cloud-storage-remote-access.md)
- [可视化Docker Compose管理工具，NAS极简部署Compose Craft，无公网内网穿透远程访问](compose-craft-docker-visualization-nas-deployment-internal-network-remote-access.md)
- [开源电商平台C-Shopping完整部署指南，Next.js+MongoDB+Docker容器化快速搭建](c-shopping-deployment-guide-nextjs-mongodb-docker.md)
