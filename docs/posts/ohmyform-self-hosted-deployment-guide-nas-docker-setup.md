---
title: "开源表单建站工具 OhMyForm 自建部署指南，NAS Docker 极简安装方案"
date: 2025-10-16
description: "OhMyForm 是 TypeForm 的开源替代方案，提供强大的表单构建、问卷调查、数据收集功能。支持多语言、11种问题类型、数据导出、内置分析、WebHook集成等。完全开源可自部署，数据安全隐私有保障。本文详细介绍 Docker 部署步骤、容器配置、端口映射、环保变量设置及访问方法。"
canonical: https://www.passnat.com/posts/ohmyform-self-hosted-deployment-guide-nas-docker-setup
---

## 项目介绍

OhMyForm 是一个开源的表单构建平台，是 TypeForm 的完全开源替代方案。它能够帮助你轻松创建美观、响应式的表单、问卷调查和数据收集工具。无论是企业数据收集、用户反馈还是在线问卷，OhMyForm 都能胜任。

项目采用现代化技术栈构建，支持多语言、11 种问题类型、数据导出、内置分析、表单嵌入、WebHook 集成等强大功能。最重要的是，作为完全开源项目，你可以自由部署在自己的服务器上，数据完全掌握在自己手中，无需担心隐私问题。

项目地址： [点我跳转](https://github.com/ohmyform/ohmyform)

## 部署安装

如果需要在外网也能访问你的表单系统，建议配置内网穿透服务。可以选择 [帕斯内网穿透](https://passnat.com) 来实现远程访问，先注册账号备用。具体的使用方法可以参考 [全平台上手指南](https://www.passnat.com/posts/p)，轻松搞定外网访问问题。

### 最快速上手方案

打开你的 NAS 设备或云服务器的 Docker 管理面板，点击本地镜像 - 添加镜像 - 从 URL 添加。

镜像地址填入：ohmyform/ohmyform:latest

等待镜像下载完成后，点击启动按钮创建容器。

### 容器配置说明

**基础设置**

勾选开机自动启动选项，确保服务重启后能自动恢复。

**端口映射**

需要映射以下端口：

容器内端口 3000 → 主机端口 3000（应用访问端口）

如果你的主机 3000 端口已被占用，可以修改主机端口号（如改为 3001、3002 等），但容器内端口必须保持为 3000 不变。

**存储配置**

建议创建以下目录用于数据持久化：

/pg\_data - 数据库存储目录（用于保存表单数据、用户信息等）

在 NAS 中创建相应的文件夹，然后将其挂载到容器内对应路径。

**环境变量设置**

必须设置的环境变量：

CREATE\_ADMIN: TRUE （创建默认管理员账户）

DATABASE\_DRIVER: postgres （使用 PostgreSQL 数据库）

DATABASE\_URL: postgresql://root:root@db:5432/ohmyform （数据库连接地址）

SECRET\_KEY: 12345 （应用密钥，建议修改为复杂字符串以增强安全性）

可选设置的环境变量：

LOGIN\_NOTE: 登录页面提示信息（默认为空）

MAILER\_URI: 邮件服务配置（如需发送邮件通知，配置 SMTP 地址）

其他变量保持默认即可，无需修改。

**启动容器**

完成上述配置后点击启动，等待容器启动完成。

### 访问应用

容器启动成功后，在浏览器中访问 http://你的服务器IP:3000

使用默认账号登录：

用户名：root

密码：root

登录后即可开始创建你的第一个表单了。

本篇教程到此结束，祝你使用愉快！如有问题可以加入项目的 Discord 社区获取帮助。

---

原文：<https://www.passnat.com/posts/ohmyform-self-hosted-deployment-guide-nas-docker-setup>
