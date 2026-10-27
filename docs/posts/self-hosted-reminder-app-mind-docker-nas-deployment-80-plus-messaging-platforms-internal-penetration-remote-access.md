---
title: "自托管提醒应用MIND，Docker极简部署NAS，80+平台消息推送，内网穿透远程访问"
date: 2025-10-16
description: "MIND是轻量级自托管提醒应用，集成Apprise API支持80+平台消息推送。提供精确到秒级通知、跨时区运行、Docker容器化部署。支持邮件、短信、即时通讯等多渠道提醒，界面简洁友好，配备完整管理后台和API接口，满足个人和团队提醒需求。"
canonical: https://www.passnat.com/posts/self-hosted-reminder-app-mind-docker-nas-deployment-80-plus-messaging-platforms-internal-penetration-remote-access
---

## 项目介绍

MIND 是一款轻量级、功能强大的自托管提醒应用，专为需要精准定时提醒的用户设计。它集成了 Apprise API，支持超过 80+ 个平台的消息推送，包括邮件、短信、即时通讯工具等。无论是生日提醒、工作计划还是日常事务，MIND 都能帮助你按时不误地接收通知。

项目采用 Docker 容器化部署，支持跨时区运行，提供精确到秒级的通知发送。界面简洁友好，支持移动设备访问，同时配备完整的管理后台和 API 接口，满足个人和小型团队的各种提醒需求。

项目地址：[点我跳转](https://github.com/Casvt/Noted)

## 部署安装

如果需要在外网访问 MIND，建议配置内网穿透服务。您可以选择[帕斯内网穿透](https://passnat.com)来实现远程访问，先注册账号备用。具体操作步骤可参考[内网穿透全平台快速入门指南](https://www.passnat.com/posts/p)。

### 使用 Docker 快速部署

打开您的 NAS 设备或服务器的 Docker 管理界面

点击**本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：casvt/noted:latest

等待镜像下载完成后，点击**启动**按钮启动容器

容器配置说明：

勾选**开机自启**选项，确保服务重启后自动运行

端口映射设置：

容器内部运行在5000端口，请将其映射到主机的任意可用端口（如5000:5000）。如果遇到端口被占用的情况，可以修改主机端口号，例如改为8080:5000，但容器内的5000端口需保持不变。

存储配置：

建议在 NAS 上创建一个专用文件夹（如/noted\_data）用于存储应用数据和备份文件，然后将其映射到容器内的/app/data目录。

环境变量设置（可选）：

根据需要配置以下环境变量：

变量名: TZ | 说明: 时区设置 | 默认值: UTC

变量名: DEBUG | 说明: 调试模式 | 默认值: false

其他配置项保持默认即可，无需特殊调整。

完成配置后点击**启动**，等待容器启动成功。

访问应用：

部署成功后，在浏览器中访问http://您的NAS地址:5000即可打开 MIND 应用

首次访问时会引导您完成初始化设置，包括创建管理员账户和配置通知渠道。

本篇教程到此结束，祝您使用愉快！

---

原文：<https://www.passnat.com/posts/self-hosted-reminder-app-mind-docker-nas-deployment-80-plus-messaging-platforms-internal-penetration-remote-access>
