---
title: "开源自建客服平台Chatwoot Docker部署指南，NAS极简配置内网穿透远程访问"
date: 2025-10-16
description: "开源客户支持平台Chatwoot完整部署指南。支持多渠道对话管理、AI客服助手Captain、自托管数据隐私保护。提供Docker快速部署、环境变量配置、内网穿透访问等详细步骤，助力中小企业搭建专业客户服务体系。"
canonical: https://www.passnat.com/posts/open-source-customer-service-platform-chatwoot-docker-deployment-guide-nas-simple-configuration-internal-network-penetration-remote-access
---

## 项目介绍

Chatwoot 是一个现代化的、开源自托管的客户支持平台，是 Intercom、Zendesk、Salesforce Service Cloud 等商业方案的绝佳替代品。它专为规模化和灵活性而设计，让企业能够完全掌控客户数据，同时提供强大的多渠道对话管理工具。

Chatwoot 集成了 AI 驱动的客服助手 Captain，可以自动化处理常见问题，减轻客服团队的工作负担。平台支持网页实时聊天、邮件、Facebook、Instagram、Twitter、WhatsApp、Telegram、Line、SMS 等多种渠道，将所有客户对话集中在一个统一的收件箱中。

无论你是中小企业还是大型组织，Chatwoot 都能帮助你提供卓越的客户支持体验。项目完全开源，可以自由部署在自己的服务器上，无需担心数据隐私问题。

[点我跳转](https://github.com/chatwoot/chatwoot)

项目地址：[点我跳转](https://github.com/chatwoot/chatwoot)

## 部署安装

如果你想在办公室外也能访问 Chatwoot，建议配置内网穿透服务。你可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它支持全平台使用，具体操作方法可以 [点我查看](https://www.passnat.com/posts/p) 官方的详细教程。

Chatwoot 提供了开箱即用的 Docker 部署方案，让技术小白也能快速上手。以下是最简便的部署步骤：

### 使用 Docker 快速部署

打开你的 NAS 设备或任何支持 Docker 的系统（如飞牛 NAS、群晖等）。

进入本地镜像管理界面，点击 **添加镜像** - **从 URL 添加**。

在镜像地址栏填入：chatwoot/chatwoot:latest

等待镜像下载完成后，点击 **启动** 按钮启动容器。

### 容器配置说明

**端口映射设置：**

Chatwoot 默认运行在容器内的 **3000 端口**。你需要将其映射到宿主机的一个可用端口（建议也使用 3000 端口，如果该端口已被占用，可改为 3001、3002 等）。映射格式为：宿主机端口:3000

**存储位置配置：**

Chatwoot 需要持久化存储来保存数据库和上传的文件。建议在 NAS 上创建一个专门的文件夹（如 /chatwoot/data），然后将其映射到容器内的 /app/storage 目录，确保重启后数据不会丢失。

**环境变量设置：**

以下是 Chatwoot 的关键环境变量配置：

**必须设置：**

RAILS\_ENV: 运行环境（默认值：production）

SECRET\_KEY\_BASE: Rails 加密密钥（建议生成一个随机字符串）

POSTGRES\_HOST: PostgreSQL 数据库主机地址

POSTGRES\_USERNAME: 数据库用户名

POSTGRES\_PASSWORD: 数据库密码

**可选设置：**

REDIS\_URL: Redis 缓存服务地址（用于提升性能）

SMTP\_ADDRESS: 邮件服务器地址（用于发送通知邮件）

SMTP\_PORT: 邮件服务器端口

SMTP\_USERNAME: 邮件账户

SMTP\_PASSWORD: 邮件密码

MAILER\_SENDER\_EMAIL: 发件人邮箱地址

其他配置项可根据需要自行调整，首次部署建议只设置必要的环境变量，其他项保持默认即可。

### 访问应用

部署完成后，在浏览器中访问 http://你的NAS\_IP:3000（将端口号替换为你实际映射的端口），即可进入 Chatwoot 的初始化界面。按照提示完成账户设置，就可以开始使用了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-customer-service-platform-chatwoot-docker-deployment-guide-nas-simple-configuration-internal-network-penetration-remote-access>


## 相关文章

- [开源日程管理系统Cal.com自托管部署指南，NAS极简配置内网穿透远程访问](open-source-calendar-management-system-cal-com-self-hosted-deployment-guide-nas-simple-configuration-intranet-penetration-remote-access.md)
- [私有部署开源聊天应用VoceChat，NAS轻松搭建安全私密聊天系统，内网穿透远程访问](vocechat-private-deployment-nas-internal-network-penetration.md)
- [开源AI搜索平台Search4All部署指南 NAS服务器Docker快速安装内网穿透远程访问](search4all-nas-docker-deployment-guide-intranet-penetration-remote-access.md)
- [Scraperr开源网页爬虫工具，无代码可视化界面自托管部署，NAS极简配置内网穿透远程访问](scraperr-open-source-web-crawler-tool-no-code-visual-interface-self-hosted-deployment-nas-simple-configuration-intranet-penetration-remote-access.md)
- [自托管私密笔记应用Notea，Docker极简部署，云存储灵活配置，内网穿透远程访问](self-hosted-private-notes-notea-docker-deployment-cloud-storage-remote-access.md)
