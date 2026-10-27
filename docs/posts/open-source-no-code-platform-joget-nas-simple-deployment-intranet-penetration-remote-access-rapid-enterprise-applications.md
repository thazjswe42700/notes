---
title: "开源无代码平台Joget，NAS极简部署，内网穿透远程访问，快速构建企业应用"
date: 2025-10-16
description: "Joget是开源无代码低代码应用平台，融合快速应用开发、业务流程自动化和工作流管理。支持可视化设计、移动端云端部署、第三方集成和应用市场。通过Joget将应用上市时间从数月缩短至数天，赋能业务技术团队协作构建企业级应用。"
canonical: https://www.passnat.com/posts/open-source-no-code-platform-joget-nas-simple-deployment-intranet-penetration-remote-access-rapid-enterprise-applications
---

## 项目介绍

Joget 是一个新一代开源无代码/低代码应用平台，专为加速数字化转型而设计。它将快速应用开发、业务流程自动化和工作流管理完美融合在一个简洁灵活的开放平台中，使业务和技术团队能够协作快速构建功能完整的企业级应用。

该平台采用网页可视化设计理念，赋能非技术人员随时随地构建和维护应用程序。通过Joget，您可以将应用上市时间从数月缩短至数周甚至数天，构建的应用原生支持移动端和云端部署。平台还提供丰富的API接口和插件架构，支持与第三方系统无缝集成。此外，Joget Marketplace 应用市场为企业提供了即插即用的解决方案。

项目地址：[点我跳转](https://github.com/jogetworkflow/jw-community)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透服务。您可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，随后参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p) 进行配置，让您无论身在何处都能流畅访问。

### 最快速部署方案

打开您的 NAS 设备（飞牛 NAS 或其他支持 Docker 的 NAS）

点击**本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：joget/joget:latest

等待镜像下载完成后，点击**启动**按钮启动容器。

### 容器配置说明

#### 开机自启设置

勾选**开机自动启动**选项，确保 NAS 重启后容器自动运行。

#### 端口映射配置

将容器内的 **8080 端口**映射到主机端口。如果您的 NAS 上 8080 端口已被占用，可以修改为其他端口（如 8081、8082 等），但容器内部端口保持 8080 不变。映射关系示例：8080:8080 或 8081:8080。

#### 存储位置配置

如需持久化保存应用数据和配置，建议在 NAS 上创建一个专用文件夹（例如 /data/joget），然后将其映射到容器内的 /opt/joget/joget-server 目录，确保数据在容器重启后不会丢失。

#### 环境变量设置

Joget 通常无需额外的环境变量配置即可正常运行，保持默认设置即可。

完成以上配置后，点击**启动**按钮。

### 访问应用

容器启动成功后，在浏览器中访问 http://NAS\_IP:8080（将 NAS\_IP 替换为您的 NAS 实际 IP 地址，端口号根据您的映射配置调整），即可进入 Joget 管理界面，开始构建您的无代码应用。

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-no-code-platform-joget-nas-simple-deployment-intranet-penetration-remote-access-rapid-enterprise-applications>
