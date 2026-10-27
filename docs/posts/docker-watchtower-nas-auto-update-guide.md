---
title: "Docker容器自动更新工具Watchtower部署指南，NAS一键启用容器自动检测升级功能"
date: 2025-10-16
description: "Watchtower是一款自动化Docker容器更新工具，能持续监控基础镜像，发现新版本时自动更新容器。完全开箱即用，支持Docker Hub和私有仓库，适合家庭实验室和本地开发环境。本文详细介绍Watchtower的快速部署步骤、端口配置、存储卷映射和环境变量设置方法，帮助用户轻松实现容器自动化管理。"
canonical: https://www.passnat.com/posts/docker-watchtower-nas-auto-update-guide
---

## 项目介绍

Watchtower 是一个强大的 Docker 容器自动化更新工具。它能够持续监控你的 Docker 容器所使用的基础镜像，当发现新版本可用时，会自动拉取最新镜像、优雅地关闭现有容器，并使用原始的部署配置重新启动容器。这意味着你无需手动干预，就能让所有容器始终保持最新状态。

特别适合在家庭实验室、媒体中心、本地开发环境等场景使用。Watchtower 支持多种镜像源，包括 Docker Hub 和私有镜像仓库，完全开箱即用，让容器管理变得简单高效。

项目地址：[点我跳转](https://github.com/containrrr/watchtower)

## 部署安装

如果你需要在外网访问部署在家中的 Watchtower，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的操作指南可以 [点击这里查看](https://www.passnat.com/posts/p)，包含全平台上手攻略。

### 快速部署（Docker 单命令）

Watchtower 提供了最简便的部署方式，只需一条命令即可启动：

```bash
docker run --detach \
    --name watchtower \
    --volume /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower
```

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理界面。

**第一步：添加镜像**

点击'本地镜像' - '添加镜像' - '从 URL 添加'

镜像地址填写：containrrr/watchtower:latest

等待镜像下载完成

**第二步：启动容器**

点击启动按钮启动容器

勾选'开机自动启动'，确保 NAS 重启后容器能自动运行

**第三步：端口配置**

如果你需要使用 HTTP API 功能，可以将容器内的 8080 端口映射到主机

端口映射规则：主机端口:8080（例如 8080:8080）

如果 8080 端口已被占用，可以修改主机端口号，但容器内端口保持 8080 不变

**第四步：存储卷配置**

容器需要访问 Docker 守护进程的 socket 文件

创建卷映射：/var/run/docker.sock:/var/run/docker.sock

这是必需的，Watchtower 通过此文件与 Docker 通信

**第五步：环境变量设置（可选）**

以下是常用的环境变量配置：

**必须设置：**

WATCHTOWER\_SCHEDULE：检查更新的时间间隔（默认：每天凌晨3点），格式为 cron 表达式

**选择设置（可不填）：**

WATCHTOWER\_CLEANUP：自动清理旧镜像（值：true 或 false，默认：false）

WATCHTOWER\_INCLUDE\_STOPPED：同时检查已停止的容器（值：true 或 false，默认：false）

WATCHTOWER\_REMOVE\_VOLUMES：删除容器时同时删除卷（值：true 或 false，默认：false）

WATCHTOWER\_DEBUG：启用调试模式（值：true 或 false，默认：false）

其他配置都可以保持默认值，直接下一步启动即可。

**第六步：完成部署**

点击启动按钮，容器开始运行

Watchtower 会在后台持续监控你的容器

部署完成后，如果你配置了 HTTP API，可以通过 http://你的NAS地址:8080 访问 API 接口。

## 总结

通过本教程，你已经成功部署了 Watchtower 容器自动更新工具。现在，你的所有 Docker 容器都能够自动保持最新状态，无需手动更新。这对于长期运行的家庭服务器来说，既能保证安全性，又能减少运维工作量。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/docker-watchtower-nas-auto-update-guide>
