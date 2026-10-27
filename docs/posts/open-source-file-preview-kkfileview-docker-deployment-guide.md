---
title: "开源文件预览服务kkFileView Docker部署完整指南，支持百余种格式在线查看"
date: 2025-10-16
description: "kkFileView是一款功能强大的开源文件在线预览解决方案，基于Spring Boot构建，支持100+种文件格式在线预览。无需下载即可快速查看Office文档、PDF、CAD图纸、3D模型、音视频等多种文件类型。提供Docker快速部署、REST API接口、自动缓存优化等功能，是企业文件管理和NAS存储管理的完整解决方案。"
canonical: https://www.passnat.com/posts/open-source-file-preview-kkfileview-docker-deployment-guide
---

## 项目介绍

kkFileView 是一款功能强大的文件在线预览解决方案，基于流行的 Spring Boot 框架构建，开箱即用、部署简单。这是一个开源的通用文件预览服务，支持超过 100+ 种文件格式的在线预览，无需下载到本地即可快速查看文件内容。

无论是办公文档（Word、Excel、PPT）、PDF、压缩包、图片、音视频，还是专业的 CAD 图纸、3D 模型、代码文件等，kkFileView 都能轻松胜任。特别是对于企业内部文件管理、NAS 存储管理等场景，kkFileView 提供了一套完整的预览解决方案，让你的文件管理变得更加便捷高效。

项目地址：[点我跳转](https://github.com/kekingcn/kkFileView)

## 部署安装

如果你想在公网上随时随地访问 kkFileView，建议配置内网穿透功能。这样即使在外出时，也能通过公网地址安全地访问家中或公司的文件预览服务。推荐使用 [帕斯内网穿透](https://passnat.com) 来实现这一功能，先注册账号备用。具体的操作步骤可以参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，轻松几步即可完成配置。

### Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板，点击 **本地镜像 - 添加镜像 - 从 URL 添加**，输入以下镜像地址：

```
keking/kkfileview:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

### 容器配置步骤

**第一步：基础设置**

勾选 **开机自动启动** 选项，这样 NAS 重启后容器会自动运行，无需手动干预。

**第二步：端口映射**

kkFileView 默认运行在容器内的 **8012 端口**。在端口设置中，建议将宿主机的 8012 端口映射到容器的 8012 端口。如果你的系统中 8012 端口已被占用，可以修改宿主机端口号（如改为 8013），但容器内部端口保持 8012 不变。

端口映射示例：**8012:8012** 或 **8013:8012**（如果 8012 被占用）

**第三步：存储配置**

kkFileView 会生成缓存文件和预览文件。建议在 NAS 上创建一个专门的文件夹（如 **/data/kkfileview**）用于存储这些数据，将其映射到容器内的 **/opt/kkFileView-4.4.0/** 目录。这样即使容器重启，预览缓存也不会丢失。

**第四步：环境变量配置**

kkFileView 支持通过环境变量进行配置，以下是常用的可选环境变量：

**可选设置：**

KKFILEVIEW\_BIN\_FOLDER：kkFileView 二进制文件夹路径（默认：/opt/kkFileView-4.4.0/bin）

file.encoding：文件编码方式（默认：UTF-8）

其他配置项均可保持默认值，无需特殊设置。直接下一步启动容器即可。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS地址:8012/
```

如果配置了内网穿透，也可以通过公网地址访问。稍等片刻，kkFileView 的首页就会加载完成，你就可以开始上传和预览各种文件了。

## 使用提示

文件上传：在首页可以直接上传文件进行预览，支持本地上传和 URL 预览

格式支持：包括 Office 全套文档、PDF、CAD 图纸、3D 模型、压缩包、图片、音视频等 100+ 种格式

性能优化：kkFileView 会自动缓存预览结果，多次预览同一文件时速度更快

REST API：项目提供了完整的 REST API，可集成到其他应用中

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-file-preview-kkfileview-docker-deployment-guide>
