---
title: "Tellor轻量级任务看板应用自托管部署教程，Docker快速安装配置指南"
date: 2025-10-16
description: "Tellor是轻量级看板待办应用，采用原生JavaScript和PHP开发，无需复杂框架。支持Trello导入、快速加载、移动友好，是自托管任务管理的完美方案。本文详细介绍Tellor部署安装步骤，包括Docker快速部署、端口配置、存储映射等完整指南，助你快速搭建个人任务管理系统。"
canonical: https://www.passnat.com/posts/tellor-lightweight-task-board-self-hosted-deployment-docker-installation-guide
---

## 项目介绍

Tellor 是一款极简主义的单用户看板待办事项应用，它是 Trello 的轻量级替代方案。项目采用纯净的设计理念，去除了繁琐的功能堆砌，只保留核心必需功能。Tellor 使用原生 JavaScript 和 PHP 开发，不依赖任何复杂框架，代码简洁易懂，非常适合个人或小团队使用。

该应用加载速度快、界面简洁紧凑、移动端友好，支持从 Trello 导入看板，让你可以快速迁移已有的任务管理数据。无需复杂的身份验证，开箱即用，是一个完美的自托管任务管理解决方案。

项目地址：[点我跳转](https://github.com/Voldrix/Tellor)

## 部署安装

如果你需要在外网也能访问这个项目，建议配置内网穿透服务。可以前往[帕斯内网穿透](https://passnat.com)注册账号，这样无论身在何处都能随时管理你的任务。具体的操作步骤可以[点击这里查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略。

### 使用 Docker 快速部署

打开你的 NAS 设备或服务器管理面板

点击**本地镜像**- **添加镜像**- **从 URL 添加**

镜像填写：`php:7-apache`（Tellor 基于 PHP 7 和 Apache 构建）

等待镜像下载完成后点击**启动**按钮启动容器

**容器配置步骤：**

勾选**开机自动启动**后进入下一步

**端口设置：**

容器内端口：`80`（Apache 默认端口）

主机端口：`8080`（如果 8080 被占用，可以改为其他端口如 8081、8082 等，但容器内端口保持 80 不变）

**存储位置：**

需要创建一个文件夹用于存储应用文件和数据库数据，建议在 NAS 上创建名为`tellor`的文件夹，然后将其映射到容器内的`/var/www/html`目录，用于保存 Tellor 应用和数据库文件。

**环境变量设置：**

由于 Tellor 是单用户应用，无需复杂的环境配置。如果需要自定义数据库凭证，可选择设置以下变量：

选择设置（可以不填）：

`DB\_USER`：数据库用户名（默认：root）

`DB\_PASSWORD`：数据库密码（默认：password）

`DB\_NAME`：数据库名称（默认：tellor）

其他配置保持默认即可，直接下一步启动容器。

**访问应用：**

容器创建成功后，在浏览器中访问`http://你的NAS地址:8080`即可看到 Tellor 应用界面

到此部署完成，你可以开始创建和管理你的任务看板了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/tellor-lightweight-task-board-self-hosted-deployment-docker-installation-guide>
