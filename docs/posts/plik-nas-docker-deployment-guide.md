---
title: "Plik私有文件分享系统NAS部署指南，Docker快速搭建临时上传平台"
date: 2025-10-16
description: "Plik是一款Golang开发的临时文件上传系统，提供类似WeTransfer的文件分享体验。支持多种存储后端、密码保护、自定义过期时间、流式传输等功能。本文详细介绍Plik在NAS上的Docker部署方法，包括端口配置、存储设置、环境变量配置等完整步骤，帮助用户快速搭建私有文件分享平台。"
canonical: https://www.passnat.com/posts/plik-nas-docker-deployment-guide
---

## 项目介绍

Plik 是一个功能强大的临时文件上传系统，采用 Golang 开发，具有高度的可扩展性和用户友好的界面。它提供了类似 WeTransfer 的文件分享体验，支持多种存储后端和元数据存储方案。

Plik 集成了强大的命令行客户端、美观易用的 Web 界面，支持文件一次性下载后自动删除、流式传输、自定义过期时间、密码保护等多项功能。无论是个人用户还是企业部署，Plik 都能提供灵活的解决方案。项目支持本地存储、OpenStack Swift、Amazon S3、Google Cloud Storage 等多种数据后端，以及 SQLite3、PostgreSQL、MySQL 等元数据存储方案。

项目地址： [点我跳转](https://github.com/root-gg/plik)

## 部署安装

如果需要在外网访问您的 Plik 实例，建议配置内网穿透服务。您可以选择 [帕斯内网穿透](https://passnat.com) 来实现这一功能，先注册好账号以备后用。具体的配置步骤可以参考 [这份详细攻略](https://www.passnat.com/posts/p)，涵盖了全平台的上手指南。

### Docker 快速部署

打开您的 NAS 设备（飞牛 NAS、群晖或其他支持 Docker 的 NAS）

点击本地镜像 - 添加镜像 - 从 URL 添加

在镜像地址栏填入：rootgg/plik:latest

等待镜像下载完成后点击启动按钮启动容器

端口配置

在容器启动配置中，需要设置端口映射。Plik 默认运行在容器内的 **8080 端口**。

宿主机端口：可自定义（如 8080、9000 等）

容器端口：8080（保持不变）

如果您的宿主机 8080 端口已被占用，可以修改宿主机端口为其他值，比如 9000，这样就可以通过 http://NAS\_IP:9000 访问 Plik。

存储配置

Plik 需要持久化存储上传的文件。建议创建一个文件夹用于存储数据：

在 NAS 上创建文件夹，例如：/mnt/plik-data

在容器配置中将此路径映射到容器内的 /home/plik/data 目录

这样可以确保即使容器重启，您上传的文件也不会丢失。

环境变量设置

以下是 Plik 的主要环境变量配置：

必须设置：

PLIKD\_DATA\_BACKEND\_CONFIG：数据存储配置，JSON 格式。例如：{"Directory":"/home/plik/data"}

可选设置：

PLIKD\_DEBUG\_REQUESTS：调试模式，默认 false

PLIKD\_LISTEN\_ADDR：监听地址，默认 0.0.0.0:8080

PLIKD\_AUTHENTICATION：启用身份验证，可选值：disabled/enabled/forced

其他配置项保持默认即可，无需特殊修改。

勾选开机自动启动

在容器启动配置完成后，勾选'开机自动启动'选项，确保 NAS 重启后 Plik 能够自动启动。

点击下一步完成配置，容器将自动启动。

访问应用

创建成功后，您可以通过以下地址访问 Plik：

```
http://NAS_IP:8080
```

（如果修改了宿主机端口，请使用对应的端口号）

此时您应该能看到 Plik 的 Web 界面，可以开始上传和分享文件了。

## 功能特性

多种上传模式：支持一次性下载、流式传输、可删除等多种模式

安全保护：支持密码保护、自定义过期时间、源 IP 限制等

灵活认证：支持本地账户、Google、OVH 等多种认证方式

强大的命令行工具：支持加密、压缩、归档等高级功能

多后端支持：支持本地存储、S3、Google Cloud Storage 等

跨平台兼容：提供 Linux、Windows、macOS 等多平台的客户端

## 总结

Plik 是一个功能完整、部署简便的文件分享解决方案。通过 Docker 一键部署，您可以在 NAS 上快速搭建自己的私有文件分享平台。无论是个人使用还是团队协作，Plik 都能提供稳定可靠的服务。

本篇教程到此结束，祝您使用愉快！

---

原文：<https://www.passnat.com/posts/plik-nas-docker-deployment-guide>


## 相关文章

- [私有Beancount记账系统Docker快速部署指南，NAS轻松搭建个人财务管理平台](private-beancount-accounting-docker-deployment-guide-nas-personal-finance-management.md)
- [MinDoc轻量级文档管理系统Docker极简部署，Golang高性能跨平台支持，内网穿透远程安全访问](mindoc-docker-deployment-golang-intranet-penetration.md)
- [魔豆文库MOREDOC开源部署指南，Golang企业级文档管理系统，支持多格式文件预览，NAS内网穿透轻松搭建](moredoc-golang-enterprise-document-management-system-multi-format-preview-nas-intranet.md)
- [WhoDB轻量级数据库管理工具，自然语言对话查询，NAS极简部署，无公网内网穿透远程访问。](whodb-lightweight-database-management-natural-language-query-nas-simple-deployment-private-network-remote-access.md)
- [高效WebP图片服务器Go版，自动格式转换降低带宽，Docker一键部署加速网站，内网穿透远程访问优化方案](efficient-webp-image-server-go-auto-format-conversion-bandwidth-reduction-docker-one-click-deployment-website-acceleration-intranet-penetration-remote-access-optimization.md)
