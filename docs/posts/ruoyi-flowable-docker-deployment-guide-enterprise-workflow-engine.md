---
title: "RuoYi-Flowable开源工作流管理平台Docker快速部署指南，企业级流程引擎解决方案"
date: 2025-10-16
description: "RuoYi-Flowable是基于RuoYi-Vue和Flowable的开源工作流管理平台，集成权限管理、在线流程设计、表单设计等企业级功能。支持Vue、Spring Boot、Redis等技术栈，适合审批流程、请假管理等复杂业务场景，提供Docker快速部署方案。"
canonical: https://www.passnat.com/posts/ruoyi-flowable-docker-deployment-guide-enterprise-workflow-engine
---

## 项目介绍

RuoYi-Flowable 是一个基于 RuoYi-Vue 和 Flowable 6.8.x 构建的开源工作流管理平台。该项目融合了 RuoYi 强大的权限管理体系与 Flowable 业界领先的流程引擎能力，为企业提供了一套完整的工作流解决方案。

前端采用 Vue 和 Element UI 技术栈，后端基于 Spring Boot、Spring Security、Redis 和 JWT 认证机制，支持多终端认证。平台内置了在线流程设计器、在线流程表单设计器、多实例会签任务、动态候选人配置等企业级功能，让你能够轻松构建复杂的业务流程系统。

无论是审批流程、请假管理还是复杂的多部门协作流程，RuoYi-Flowable 都能胜任。项目代码开源，社区活跃，适合企业二次开发和定制化部署。

项目地址：[点我跳转](https://gitee.com/tony2y/RuoYi-flowable)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的详细教程可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台快速上手指南。

RuoYi-Flowable 项目为 Java 后端应用，推荐使用 Docker 部署，这样可以快速启动而无需复杂的环境配置。

### 快速部署步骤

打开你的 NAS 设备（如飞牛 NAS、群晖等）或服务器的 Docker 管理面板。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**。

在镜像地址栏填入以下镜像地址：

```
tony2y/ruoyi-flowable:latest
```

等待镜像下载完成。下载过程可能需要几分钟，请耐心等待。

镜像下载完成后，点击 **启动** 按钮启动容器。

### 容器配置详解

#### 基础配置

勾选 **开机自动启动**，确保系统重启后容器能够自动运行。

#### 端口映射设置

RuoYi-Flowable 默认运行在容器内的 **8080 端口**。你需要将其映射到主机的某个端口。建议设置为：

容器内端口：'8080'

主机端口：'8080'（如果主机 8080 端口已被占用，可改为 '8888'、'9999' 等其他可用端口）

如果提示端口被占用，只需修改主机端口号即可，容器内的端口保持不变。

#### 存储挂载配置

RuoYi-Flowable 需要持久化存储数据库和日志文件。建议创建以下目录映射：

容器内路径：'/home/ruoyi/logs' → 主机路径：'/mnt/data/ruoyi/logs'（用于存储应用日志）

容器内路径：'/home/ruoyi/upload' → 主机路径：'/mnt/data/ruoyi/upload'（用于存储上传文件）

#### 环境变量配置

环境变量设置：必须配置

```
MYSQL_HOST: 数据库主机地址（默认：localhost）
MYSQL_PORT: 数据库端口（默认：3306）
MYSQL_USER: 数据库用户名（默认：root）
MYSQL_PASSWORD: 数据库密码（建议修改为强密码）
MYSQL_DB: 数据库名称（默认：ry-flowable）
```

选择设置，可以不填

```
REDIS_HOST: Redis 缓存主机地址（默认：localhost）
REDIS_PORT: Redis 端口（默认：6379）
REDIS_PASSWORD: Redis 密码（如果 Redis 无密码可不填）
SERVER_PORT: 应用服务端口（默认：8080）
```

其他参数保持默认即可，无需修改。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的服务器IP:8080
```

例如本地访问：'http://192.168.1.100:8080' 或 'http://localhost:8080'

首次登录使用默认账号：

用户名：'admin'

密码：'admin123'

**强烈建议首次登录后立即修改密码！**

### 常见问题

Q：容器无法启动怎么办？

A：检查数据库和 Redis 是否正常运行。如果使用的是单独部署的数据库，确保网络连通性，检查环境变量中的数据库地址和密码是否正确。

Q：如何查看应用日志？

A：通过 Docker 管理面板查看容器日志，或进入主机的日志挂载目录 '/mnt/data/ruoyi/logs' 查看详细日志文件。

Q：流程设计器无法使用？

A：确保前端静态资源已正确加载。清除浏览器缓存后重新访问，或检查浏览器控制台是否有报错信息。

本篇教程结束，祝你部署顺利！

---

原文：<https://www.passnat.com/posts/ruoyi-flowable-docker-deployment-guide-enterprise-workflow-engine>
