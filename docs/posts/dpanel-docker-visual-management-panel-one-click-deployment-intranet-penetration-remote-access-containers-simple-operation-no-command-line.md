---
title: "DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行"
date: 2025-10-16
description: "DPanel是功能强大的Docker可视化管理面板，提供容器管理、镜像管理、文件管理、Compose编排等完整功能。无需命令行知识，通过直观Web界面即可轻松管理容器化应用。支持一键快速部署、轻量级版本和脚本安装多种方式。"
canonical: https://www.passnat.com/posts/dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line
---

## 项目介绍

DPanel 是一个功能强大的 Docker 可视化管理面板系统，为用户提供了完善的 Docker 容器管理功能。无论你是 Docker 初学者还是经验丰富的开发者，DPanel 都能帮助你轻松管理和部署容器化应用。

该项目采用现代化技术栈构建，集成了容器管理、镜像管理、文件管理、Compose 编排等多项功能于一身。通过直观的 Web 界面，用户可以无需掌握复杂的命令行知识，就能快速上手 Docker 的各项操作。无论是监控容器状态、管理镜像库，还是部署多容器应用，DPanel 都能提供便捷的解决方案。

项目地址： [点我跳转](https://github.com/donknap/dpanel)

## 部署安装

如果您需要在外网访问部署的 DPanel 面板，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的配置方法可以参考 [帕斯内网穿透全平台快速指南](https://www.passnat.com/posts/p)，让您随时随地都能管理自己的 Docker 环境。

### 快速部署（推荐）

DPanel 提供了最简便的一键部署方案。对于技术小白用户，只需执行一条命令即可完成安装：

```bash
docker run -d --name dpanel --restart=always \
 -p 80:80 -p 443:443 -p 8807:8080 -e APP_NAME=dpanel \
 -v /var/run/docker.sock:/var/run/docker.sock -v dpanel:/dpanel \
 dpanel/dpanel:latest
```

国内用户加速版本**（推荐国内用户使用）**：

```bash
docker run -d --name dpanel --restart=always \
 -p 80:80 -p 443:443 -p 8807:8080 -e APP_NAME=dpanel \
 -v /var/run/docker.sock:/var/run/docker.sock -v dpanel:/dpanel \
 registry.cn-hangzhou.aliyuncs.com/dpanel/dpanel:latest
```

### 轻量级部署

如果您只需要基础的容器管理功能，可以选择 Lite 版本，它去掉了域名转发相关功能，无需占用 80 和 443 端口：

```bash
docker run -d --name dpanel --restart=always \
 -p 8807:8080 -e APP_NAME=dpanel \
 -v /var/run/docker.sock:/var/run/docker.sock -v dpanel:/dpanel \
 registry.cn-hangzhou.aliyuncs.com/dpanel/dpanel:lite
```

### 一键脚本安装

DPanel 还提供了集成脚本，支持 Debian、Ubuntu、Alpine 等主流 Linux 发行版。在服务器上执行以下命令即可自动完成安装：

```bash
curl -sSL https://dpanel.cc/quick.sh -o quick.sh && sudo bash quick.sh
```

## 部署参数说明

**端口映射说明：**

8807:8080 - DPanel 管理面板访问端口（容器内部为 8080，映射到宿主机 8807）

80:80 - HTTP 访问端口（标准版需要）

443:443 - HTTPS 访问端口（标准版需要）

如果您的宿主机上 8807 端口已被占用，可以修改前面的数字为其他端口，例如 8888:8080 表示通过 8888 端口访问。容器内部端口 8080 保持不变即可。

**存储卷说明：**

/var/run/docker.sock:/var/run/docker.sock - Docker 套接字文件，用于 DPanel 与 Docker 通信

dpanel:/dpanel - DPanel 数据持久化存储目录，保存所有配置和数据

**环境变量：**

APP\_NAME=dpanel - 应用名称，保持默认即可

## 访问面板

部署完成后，打开浏览器访问以下地址即可进入 DPanel 管理面板：

```
http://您的服务器IP:8807
```

例如，如果您在本地部署，则访问 http://localhost:8807 即可看到 DPanel 的可视化管理界面。

## 功能特性

容器管理 - 实时查看、启动、停止、删除容器

镜像管理 - 浏览、拉取、构建、删除 Docker 镜像

文件管理 - 在线编辑容器内文件

Compose 编排 - 可视化创建和部署 Docker Compose 配置

系统监控 - 实时监控 Docker 系统状态和资源使用

## 特殊说明

**macOS 用户注意**：如果您在 macOS 上使用 DPanel，需要先执行以下命令将 Docker 套接字文件链接到标准位置：

```bash
sudo ln -s -f /Users/用户/.docker/run/docker.sock /var/run/docker.sock
```

将 用户 替换为您的实际用户名。

本篇教程结束。祝您使用愉快！

---

原文：<https://www.passnat.com/posts/dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line>


## 相关文章

- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [自托管即时通讯平台Stoat完整部署指南，内网穿透远程访问无公网限制](self-hosted-stoat-instant-messaging-deployment-guide-intranet-penetration-remote-access-no-public-ip.md)
- [免费在线虚拟钢琴演奏平台 AutoPiano NAS 极简部署 内网穿透远程弹奏](free-online-virtual-piano-autopiano-nas-simple-deployment-intranet-penetration-remote-playing.md)
- [Yacht容器管理系统一键部署指南，Docker应用模板快速启动，NAS自建服务完整教程](yacht-container-management-one-click-deployment-guide-docker-application-templates-quick-start-nas-self-built-services-complete-tutorial.md)
- [容器管理神器Yacht一键部署指南，Docker应用模板化管理简单易用，支持内网穿透远程访问管理](yacht-docker-container-management-deployment-guide.md)
