---
title: "开源自主翻译API LibreTranslate Docker部署指南 NAS一键搭建私人翻译服务"
date: 2025-10-16
description: "本文介绍LibreTranslate开源翻译API的部署方法。LibreTranslate采用开源Argos引擎，完全自主部署，无需依赖Google等商业服务，保护隐私且稳定可靠。文章详细讲解Docker快速部署步骤，包括镜像添加、端口映射、存储配置和环境变量设置，适合个人和企业用户搭建私有翻译服务。"
canonical: https://www.passnat.com/posts/libre-translate-docker-nas-deployment-guide
---

## 项目介绍

LibreTranslate 是一款完全开源、自主部署的机器翻译 API 服务。与其他翻译服务不同，它不依赖于 Google、Azure 等专有商业翻译提供商，而是采用开源的 Argos Translate 引擎驱动。这意味着你可以完全掌控自己的翻译服务，无需担心隐私泄露或服务中断。无论是个人使用还是企业部署，LibreTranslate 都能提供稳定、可靠的多语言翻译能力。

项目地址：[点我跳转](https://github.com/LibreTranslate/LibreTranslate)

## 部署安装

如果需要在家外也能访问该服务，建议配置内网穿透方案。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号备用。具体的配置方法可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手指南。

LibreTranslate 提供了开箱即用的 Docker 部署方式，让技术小白也能轻松搭建属于自己的翻译服务。

### 使用 Docker 快速部署

打开你的 NAS 设备（飞牛、群晖等均支持）

点击 **本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：libretranslate/libretranslate:latest

等待镜像下载完成后，点击 **启动** 按钮启动容器。

### 端口映射设置

容器内部使用 5000 端口提供翻译服务。你可以将其映射到主机的任意端口，建议直接映射为 5000:5000。如果你的主机 5000 端口已被占用，可以修改为其他端口（如 8888:5000），但容器内端口必须保持为 5000。

### 存储配置（可选）

如果你想要保留 API 密钥和模型文件，建议创建两个持久化存储目录：

/app/db - 用于存储 API 密钥数据库
/home/libretranslate/.local - 用于缓存翻译模型，避免每次重启都重新下载

### 环境变量设置

必须设置：

无强制要求的环境变量

可选设置（根据需要启用）：

LT\_API\_KEYS=true - 启用 API 密钥功能，用于限制访问权限
LT\_API\_KEYS\_DB\_PATH=/app/db/api\_keys.db - 指定 API 密钥数据库路径
LT\_UPDATE\_MODELS=true - 启用模型更新功能
LT\_LOAD\_ONLY=en,fr - 仅加载指定语言模型（用逗号分隔），可以减少内存占用

其他配置无需设置，直接启动即可。

勾选 **开机自动启动** 确保服务在 NAS 重启后自动恢复运行。

### 访问服务

部署完成后，在浏览器中访问 http://你的NAS-IP:5000 即可打开 LibreTranslate 的网页界面，开始进行文本翻译。

到这里部署就全部完成了！你现在拥有了一个完全私有、不受限制的翻译服务。

本篇教程结束

---

原文：<https://www.passnat.com/posts/libre-translate-docker-nas-deployment-guide>
