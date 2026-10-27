---
title: "离线OCR识别工具TrWebOCR，NAS一键部署，内网穿透远程访问，支持中文识别"
date: 2025-10-16
description: "TrWebOCR是开源离线OCR文字识别工具，提供HTTP接口和网页界面。支持中文识别、文字检测、旋转纠正等功能，识别速度快准确率高。采用Tornado多进程架构处理并发请求，完全离线运行保护数据隐私，可轻松集成到其他项目中使用。"
canonical: https://www.passnat.com/posts/tr-web-ocr-offline-recognition-nas-deployment-intranet-penetration-chinese-support
---

## 项目介绍

TrWebOCR 是一款开源的离线文字识别工具，基于业界知名的 Tr 项目打造。它为用户提供了便捷的 HTTP 接口调用方式，让你可以轻松在其他项目中集成 OCR 功能。同时配备了友好易用的网页界面，既适合日常调试使用，也能满足生产环节的需求。

项目具备中文识别、文字检测、支持旋转纠正等核心功能，识别速度快且准确率高。通过 Tornado 多进程架构，能够处理一定数量的并发请求，具体并发能力取决于你的硬件配置。最重要的是，TrWebOCR 完全离线运行，无需依赖任何在线 API，保护你的数据隐私。

项目地址： [点我跳转](https://github.com/alisen39/TrWebOCR)

## 部署安装

如果你需要在外网也能访问本地部署的 TrWebOCR，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作指南可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台快速入门教程。

### Docker 快速部署

打开你的飞牛 NAS 或其他 NAS 设备管理界面

点击 **本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：`mmmz/trwebocr:latest`

等待镜像下载完成后，点击 **启动** 按钮启动容器

在启动配置中进行以下设置：

端口映射设置

将容器的 **8089** 端口映射到物理机。如果 8089 端口已被占用，你可以修改左侧的物理机端口号（比如改为 8090），但容器内部端口必须保持为 **8089** 不变。

存储配置

TrWebOCR 无需特殊的持久化存储配置，使用默认设置即可。

环境变量设置

此项目无需配置环境变量，保持默认即可。

勾选 **开机自动启动**，然后点击 **启动** 完成部署。

### 访问应用

部署成功后，在浏览器中访问：`http://你的NAS地址:8089`

即可打开 TrWebOCR 的网页界面，开始使用文字识别功能。

### 系统要求

CPU: 1核及以上

内存: 2GB 及以上

交换空间: 2GB 及以上

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/tr-web-ocr-offline-recognition-nas-deployment-intranet-penetration-chinese-support>
