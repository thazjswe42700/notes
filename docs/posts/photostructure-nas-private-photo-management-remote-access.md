---
title: "家庭私人照片管理系统PhotoStructure，NAS一键部署，内网穿透远程访问你的回忆"
date: 2025-10-16
description: "PhotoStructure是功能强大的家庭照片视频管理系统，支持Docker容器化部署。提供跨平台解决方案，具备智能扫描导入、元数据管理、视频转码等功能。所有数据存储在自己服务器上，完全掌握隐私。支持NAS和Linux服务器部署，默认3000端口提供Web服务，配合内网穿透可实现远程访问。"
canonical: https://www.passnat.com/posts/photostructure-nas-private-photo-management-remote-access
---

## 项目介绍

PhotoStructure 是一个功能强大的家庭照片和视频管理系统，为你提供了一个统一的平台来存储、组织和访问所有的数字回忆。这是一个开箱即用的跨平台解决方案，支持 Docker 容器化部署，让你可以轻松在服务器上搭建属于自己的私人媒体库。

PhotoStructure 采用现代化的技术架构，支持多种操作系统和部署方式，无论你是使用桌面版本还是服务器版本，都能获得一致的用户体验。它具备智能扫描导入、元数据管理、视频转码等强大功能，让你的照片和视频得到妥善管理。最重要的是，所有数据都存储在你自己的服务器上，完全掌握在自己手中，无需担心隐私问题。

项目地址：[点我跳转](https://photostructure.com)

## 部署安装

如果你想在外网也能访问家中的 PhotoStructure 媒体库，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即使在外出时也能随时随地查看你的照片和视频。具体的配置方法可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台上手攻略，轻松实现远程访问。

### 快速部署方式

打开你的 NAS 设备或 Linux 服务器，点击本地镜像 - 添加镜像 - 从 URL 添加。

在镜像地址栏填入：photostructure/server:latest

等待镜像下载完成后，点击启动按钮启动容器。

### 容器配置说明

**开机自动启动**

勾选开机自动启动选项，这样在 NAS 重启后 PhotoStructure 会自动运行。

**端口映射设置**

PhotoStructure 默认使用 **3000 端口**提供 Web 服务。在端口设置中，将容器内的 3000 端口映射到宿主机的 3000 端口（或其他未被占用的端口）。如果你的系统中已有应用占用了 3000 端口，可以修改宿主机端口为其他值，比如 3001、8080 等，但请记住你修改后的端口号，后续访问时需要用到。

**存储位置配置**

PhotoStructure 需要持久化存储库文件和配置信息。建议在 NAS 上创建一个专用文件夹，例如 `/data/photostructure`，然后将其映射到容器内的 `/ps/library` 目录。

同时，你需要将存放照片和视频的目录也映射到容器中，例如将 `/mnt/photos` 映射到容器内的 `/photos`（只读模式），这样 PhotoStructure 就能扫描并导入你的媒体文件。

**环境变量设置**

环境变量设置（可选）：

TZ: 时区设置，默认为 `UTC`，建议改为 `Asia/Shanghai`

PS\_LOG\_LEVEL: 日志级别，默认为 `info`，可选 `debug`、`warn` 等

PUID: 运行进程的用户 ID，默认为 `0`（root）

PGID: 运行进程的用户组 ID，默认为 `0`（root）

如果你对 PhotoStructure 的默认配置满意，这些环境变量都可以不设置，直接使用默认值即可。

**启动容器**

完成上述配置后，点击下一步启动容器。容器启动需要几分钟时间，请耐心等待。

**访问应用**

创建成功后，在浏览器中访问 `http://你的NAS地址:3000`（如果你修改了端口号，请使用修改后的端口）。

首次访问时，PhotoStructure 会引导你完成初始化设置，包括接受许可协议、创建媒体库等步骤。按照提示完成设置后，你就可以开始上传和管理你的照片和视频了。

本篇教程结束。祝你使用 PhotoStructure 愉快！

---

原文：<https://www.passnat.com/posts/photostructure-nas-private-photo-management-remote-access>


## 相关文章

- [DweebUI容器管理面板轻松上手，Docker Podman全能卫士一键部署运维](dweebui-container-management-panel-docker-podman-deployment.md)
- [免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库](lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access.md)
- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [自托管即时通讯平台Stoat完整部署指南，内网穿透远程访问无公网限制](self-hosted-stoat-instant-messaging-deployment-guide-intranet-penetration-remote-access-no-public-ip.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
