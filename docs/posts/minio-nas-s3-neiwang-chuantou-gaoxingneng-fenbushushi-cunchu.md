---
title: "开源MinIO私有对象存储，NAS极简部署S3兼容，内网穿透远程访问，高性能分布式存储解决方案"
date: 2025-10-16
description: "MinIO是高性能S3兼容的分布式对象存储解决方案，完全开源采用AGPLv3许可证。支持从单机到数据中心级部署，具有自我修复、自我管理特性。提供亚10毫秒延迟和卓越吞吐量，适用于AI/ML、数据分析等工作负载。可通过Docker快速部署在NAS或私有云环境中。"
canonical: https://www.passnat.com/posts/minio-nas-s3-neiwang-chuantou-gaoxingneng-fenbushushi-cunchu
---

## 项目介绍

MinIO 是一个高性能、S3 兼容的对象存储解决方案，专为速度和可扩展性而设计。它是一个开箱即用的分布式存储系统，完全兼容 Amazon S3 API，能够为 AI/ML、数据分析和大规模数据处理工作负载提供业界领先的性能。

MinIO 采用高度优化的架构，支持从单机到数据中心级别的部署，具有自我修复、自我管理的特性，能够消除传统存储系统的复杂性。无论是用于本地 NAS 存储、私有云还是混合云环境，MinIO 都能提供一致的亚 10 毫秒延迟和卓越的吞吐量。最关键的是，MinIO 完全开源，采用 AGPLv3 许可证，任何人都可以自由部署和使用。

项目地址：[点我跳转](https://github.com/minio/minio)

## 部署安装

如果你想在外网也能访问你的存储服务，建议配置内网穿透工具。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号使用，详细的配置教程可以 [点我查看](https://www.passnat.com/posts/p)。

### 使用 Docker 快速部署

打开你的 NAS 设备或 Docker 管理界面，点击本地镜像-添加镜像-从 URL 添加。

镜像地址填写：minio/minio:latest

等待镜像下载完成后，点击启动按钮启动容器。

### 配置容器参数

**勾选开机自动启动**，然后进入下一步。

**端口映射设置**：

容器内的 9000 端口（对象存储服务）映射到主机的 9000 端口

容器内的 9001 端口（Web 管理控制台）映射到主机的 9001 端口

如果这些端口在你的主机上已被占用，可以修改映射的主机端口。例如可以改成 19000 和 19001，但要记住你修改后的端口号。

**存储位置设置**：

建议在 NAS 上创建一个专门的文件夹用于存储数据，例如 /mnt/minio/data，将其映射到容器内的 /data 目录。这样可以确保你的数据持久化保存。

**环境变量设置**：

必须设置：

MINIO\_ROOT\_USER：管理员用户名（默认：minioadmin）

MINIO\_ROOT\_PASSWORD：管理员密码（默认：minioadmin）

可选设置：

MINIO\_CONSOLE\_ADDRESS：Web 控制台地址（默认：:9001）

其他参数保持默认即可，直接点击启动。

**访问 MinIO**

容器启动成功后，在浏览器中访问：

**Web 控制台**：http://你的NAS\_IP:9001

**对象存储服务**：http://你的NAS\_IP:9000

使用你设置的用户名和密码登录（默认为 minioadmin/minioadmin）。

登录后，你就可以创建存储桶、上传文件、管理数据等操作了。MinIO 的 Web 界面简洁易用，即使是技术小白也能快速上手。

本篇教程结束。现在你已经拥有一个功能强大的私有对象存储系统，可以随时随地安全地存储和访问你的数据！

---

原文：<https://www.passnat.com/posts/minio-nas-s3-neiwang-chuantou-gaoxingneng-fenbushushi-cunchu>
