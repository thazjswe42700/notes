---
title: "统一网盘聚合管理工具PanIndex，多云存储跨平台部署，内网穿透远程访问无限资源"
date: 2025-10-16
description: "PanIndex是功能强大的跨平台网盘聚合管理工具，基于Go语言开发，支持阿里云盘、OneDrive、Google Drive等十余个云存储平台。提供统一界面管理文件，支持多种主题、文件预览、防盗链、短链分享、WebDAV等功能。部署简单快速，适合个人文件管理、资源分享和团队协作。"
canonical: https://www.passnat.com/posts/panindex-unified-cloud-storage-aggregation-tool-multi-cloud-cross-platform-deployment
---

## 项目介绍

PanIndex 是一款功能强大的跨平台网盘聚合管理工具，基于 Go 语言开发，提供开箱即用的部署体验。它支持多种主流云存储服务的接入，包括阿里云盘、OneDrive、Google Drive、115网盘、123网盘、PikPak 等十余个平台，让你可以在一个统一的界面中管理和分享来自不同云盘的文件。

项目具备丰富的功能特性：支持多种展示主题、深色模式、文件预览（图片、视频、音频、代码、PDF、EPUB、Markdown）、防盗链、短链分享、访问控制、WebDAV 等。无论是个人文件管理、资源分享还是团队协作，PanIndex 都能轻松胜任。界面简洁美观，部署简单快速，即使是技术小白也能快速上手。

项目地址： [点我跳转](https://github.com/libsgh/PanIndex)

## 部署安装

如果你需要在办公室或外出时也能访问家中的 PanIndex，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它提供了简单易用的穿透解决方案。具体的操作步骤可以参考 [帕斯内网穿透全平台上手指南](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### 使用 Docker 快速部署

打开你的 NAS 设备或任何支持 Docker 的系统，进入 Docker 管理界面。

**第一步：添加镜像**

点击'本地镜像'或'镜像管理'，选择'添加镜像'或'从 URL 添加'选项。

在镜像地址栏填入：iicm/pan-index:latest

等待镜像下载完成，这个过程可能需要几分钟，请耐心等待。

**第二步：启动容器**

镜像下载完成后，点击'启动'或'运行'按钮来启动容器。

**第三步：配置基本设置**

勾选'开机自动启动'选项，确保 NAS 重启后 PanIndex 能够自动运行。

**第四步：端口映射配置**

在端口设置界面，配置如下：

容器内端口：5238（这是 PanIndex 的默认服务端口，不建议修改）

宿主机端口：5238（如果你的 NAS 上 5238 端口已被占用，可以修改为其他端口，比如 8888、9999 等，但要记住修改后的端口号）

如果修改了宿主机端口，后续访问时需要使用修改后的端口号。

**第五步：存储配置**

PanIndex 会在容器内生成配置文件和数据库文件。建议创建一个本地文件夹用于数据持久化：

在 NAS 上创建一个文件夹，例如：/mnt/panindex

在容器配置中，将此文件夹映射到容器内的 /app/data 目录

这样即使容器重启，你的配置和数据也不会丢失

**第六步：环境变量设置**

PanIndex 提供了丰富的环境变量配置选项，根据你的需求进行设置：

**必须设置的环境变量：**

无（PanIndex 可以零配置启动）

**可选的环境变量配置：**

ADMIN\_PASSWORD：管理员密码（建议设置以保护你的实例）

PORT：服务端口（默认值：5238）

LOG\_LEVEL：日志级别（默认值：info，可选 debug/info/warn/error）

如果你需要设置管理员密码来保护 PanIndex 实例，可以添加 ADMIN\_PASSWORD 环境变量。其他变量通常保持默认即可。

**第七步：启动服务**

确认所有配置无误后，点击'启动'或'创建'按钮，容器将开始启动。

启动成功后，你可以在浏览器中访问：http://你的NAS\_IP:5238

（如果你修改了宿主机端口，请使用修改后的端口号，例如：http://你的NAS\_IP:8888）

此时你应该能看到 PanIndex 的登录或首页界面，恭喜你已经成功部署！

**第八步：配置云盘账号**

进入 PanIndex 后，在设置或管理界面中添加你想要接入的云盘账号（支持阿里云盘、OneDrive、Google Drive 等），即可开始管理和分享你的文件了。

本篇教程到此结束，祝你使用愉快！如有任何问题，欢迎查阅 [官方文档](https://docs.noki.icu) 或在 GitHub 上提出 Issue。

---

原文：<https://www.passnat.com/posts/panindex-unified-cloud-storage-aggregation-tool-multi-cloud-cross-platform-deployment>
