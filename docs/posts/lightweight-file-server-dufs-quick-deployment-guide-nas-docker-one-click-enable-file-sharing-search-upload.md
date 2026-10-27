---
title: "轻量级文件服务器 DUFS 快速部署指南 NAS Docker 一键启用文件共享搜索上传"
date: 2025-10-16
description: "DUFS是功能强大的文件服务器工具，提供开箱即用的文件共享解决方案。支持文件上传下载、搜索、权限控制、HTTPS加密、WebDAV协议等功能。采用Rust开发，性能卓越，资源占用低，可部署在NAS、服务器等各类设备上，轻松搭建企业级文件管理系统。"
canonical: https://www.passnat.com/posts/lightweight-file-server-dufs-quick-deployment-guide-nas-docker-one-click-enable-file-sharing-search-upload
---

## 项目介绍

DUFS 是一个功能强大的文件服务器工具，它提供了开箱即用的文件共享解决方案。无论你是想要快速搭建一个文件下载站点，还是需要一个支持上传、搜索、权限控制的企业级文件管理系统，DUFS 都能完美胜任。

这个项目支持静态文件服务、文件上传下载、文件夹压缩下载、文件搜索、权限访问控制、HTTPS 加密、WebDAV 协议等丰富功能。它采用 Rust 语言开发，性能卓越，资源占用低，可以轻松运行在各种硬件设备上。无论你是在 NAS、服务器还是个人电脑上部署，DUFS 都能为你提供稳定高效的文件共享服务。

[点我跳转](https://github.com/sigoden/dufs)

## 部署安装

如果你需要在外网也能访问你的文件服务器，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的使用教程可以 [点我查看](https://www.passnat.com/posts/p) 完整的上手攻略。

### Docker 单命令部署

DUFS 最简便的部署方式就是使用 Docker。打开你的 NAS 设备或服务器的 Docker 管理界面，选择'添加镜像'，从 URL 添加下列镜像：

**镜像地址：** `sigoden/dufs:latest`

等待镜像下载完成后，点击启动按钮启动容器。

### 容器配置说明

**端口映射设置：**

DUFS 默认运行在容器内的 5000 端口。在启动容器时，需要将容器内的 5000 端口映射到宿主机的某个端口（比如 5000）。如果你的宿主机 5000 端口已被占用，可以修改宿主机端口为其他值（如 8080），但容器内端口必须保持为 5000。

**存储位置配置：**

DUFS 需要访问你要共享的文件目录。在容器启动前，建议在 NAS 上创建一个文件夹（比如 `/mnt/data/share`），然后在启动容器时将这个目录映射到容器内的 `/data` 目录。这样 DUFS 就能访问你在宿主机上的所有文件。

**环境变量配置：**

DUFS 提供了丰富的环境变量选项来自定义服务行为。以下是常用的必须配置项：

**必须设置：**

DUFS\_ALLOW\_ALL: 设置为 `true` 以启用所有操作（上传、删除、搜索等），默认为 `false`

**可选配置项（根据需要设置）：**

DUFS\_PORT: 服务监听端口，默认为 `5000`

DUFS\_BIND: 绑定地址，默认为 `0.0.0.0`（所有网卡）

DUFS\_ALLOW\_UPLOAD: 允许文件上传，默认为 `false`

DUFS\_ALLOW\_DELETE: 允许删除文件，默认为 `false`

DUFS\_ALLOW\_SEARCH: 允许搜索文件，默认为 `false`

DUFS\_ALLOW\_ARCHIVE: 允许下载文件夹为 ZIP 压缩包，默认为 `false`

DUFS\_ENABLE\_CORS: 启用 CORS 跨域支持，默认为 `false`

DUFS\_AUTH: 设置用户认证规则，格式为 `用户名:密码@路径:权限`，默认不设置

DUFS\_HIDDEN: 隐藏指定文件或文件夹，用逗号分隔，如 `.git,.DS\_Store,\*.log`

如果你只是想快速体验，直接设置 `DUFS\_ALLOW\_ALL=true` 即可启用全部功能。其他变量可以根据你的具体需求进行调整。

**配置完成后启动：**

勾选'开机自动启动'选项，然后点击启动按钮。容器启动成功后，你就可以通过浏览器访问 `http://你的IP:5000` 来使用 DUFS 了。

### 访问你的文件服务器

部署完成后，打开浏览器，输入 `http://NAS的IP地址:5000` 即可访问 DUFS 的 Web 界面。你可以在这里浏览、搜索、上传、下载文件，体验完整的文件管理功能。

## 总结

DUFS 是一个轻量级但功能完整的文件服务器解决方案。通过简单的 Docker 部署，你可以在几分钟内搭建一个功能强大的文件共享平台。无论是家庭 NAS、企业服务器还是云端部署，DUFS 都能为你提供稳定高效的服务。

本篇教程到此结束，希望能帮助你快速上手 DUFS！

---

原文：<https://www.passnat.com/posts/lightweight-file-server-dufs-quick-deployment-guide-nas-docker-one-click-enable-file-sharing-search-upload>
