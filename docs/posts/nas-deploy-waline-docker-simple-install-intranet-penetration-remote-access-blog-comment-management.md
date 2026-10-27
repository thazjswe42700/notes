---
title: "NAS部署Waline评论系统，Docker极简安装，内网穿透远程访问博客评论管理"
date: 2025-10-16
description: "Waline是一款简洁安全的评论系统，支持完整Markdown语法、多种通知方式。提供243种部署组合方案，兼容LeanCloud、MongoDB、MySQL等多种数据存储。支持Docker快速部署在NAS或本地服务器上，可配置内网穿透实现远程访问，开箱即用、功能强大。"
canonical: https://www.passnat.com/posts/nas-deploy-waline-docker-simple-install-intranet-penetration-remote-access-blog-comment-management
---

## 项目介绍

Waline 是一款简洁、安全且功能强大的评论系统解决方案。它以快速、轻量、易用为核心特点，支持完整的 Markdown 语法，内置邮件、微信、QQ、Telegram 等多种通知方式。无论你是博客主还是网站运营者，Waline 都能为你的平台提供一个开箱即用、安全可靠的评论体验。

项目采用灵活的部署架构，支持包括 Vercel、CloudBase、Railway、Render、Docker 等多达 243 种部署组合方案，同时兼容 LeanCloud、MongoDB、MySQL、SQLite、PostgreSQL 等多种数据存储服务。无论你的技术栈如何，总能找到最适合的部署方式。

项目地址：[点我跳转](https://github.com/walinejs/waline)

## 部署安装

如果你需要在外部网络访问部署在家中 NAS 或本地服务器上的 Waline 评论系统，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的配置步骤可以参考 [内网穿透全平台快速上手指南](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### 使用 Docker 快速部署

打开你的飞牛 NAS、群晖 NAS 或其他支持 Docker 的 NAS 设备

点击容器管理 - 本地镜像 - 添加镜像 - 从 URL 添加

在镜像 URL 栏填入：walinesql:latest

等待镜像下载完成后，点击启动按钮创建容器

**基础配置步骤：**

勾选开机自动启动选项，确保系统重启后服务自动恢复

**端口映射设置：**

容器内部端口为 8360，这是 Waline 服务的默认监听端口。如果你的 NAS 上 8360 端口已被占用，可以修改宿主机端口（左侧数字），但容器内端口（右侧数字）需保持为 8360 不变。例如可以改为 8361:8360 或其他可用端口。

**存储卷配置：**

Waline 需要持久化存储数据库和配置文件。建议在 NAS 上创建一个专用文件夹（如 `/mnt/data/waline`），然后将其映射到容器内的 `/app/data` 目录，确保评论数据不会因为容器重启而丢失。

**环境变量设置：**

以下是 Waline 的关键环境变量配置：

必须设置：

SQLITE\_PATH：SQLite 数据库文件路径，建议设置为 `/app/data/waline.db`（使用 SQLite 作为数据库时）

可选设置（根据需要）：

ADMIN\_USER：管理员用户名，用于后台管理评论

ADMIN\_PASS：管理员密码，建议使用强密码

MAIL\_FROM：发送邮件的邮箱地址（如需启用邮件通知）

MAIL\_USER：邮箱用户名

MAIL\_PASS：邮箱密码或授权码

MAIL\_HOST：邮箱 SMTP 服务器地址

MAIL\_PORT：邮箱 SMTP 端口，通常为 465 或 587

其他环境变量可保持默认，直接下一步启动即可。

**完成部署：**

创建成功后，你可以通过访问 `http://你的NAS地址:8360` 来打开 Waline 管理后台（如果修改了端口，请使用修改后的端口号）

到此 Waline 评论系统就已成功部署完成，你可以开始配置和使用它了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/nas-deploy-waline-docker-simple-install-intranet-penetration-remote-access-blog-comment-management>
