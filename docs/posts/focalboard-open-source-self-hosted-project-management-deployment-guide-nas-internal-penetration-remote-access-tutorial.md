---
title: "开源自托管项目管理工具Focalboard部署指南，NAS内网穿透远程访问完整教程"
date: 2025-10-16
description: "Focalboard是开源自托管项目管理工具，支持macOS、Windows、Linux多平台部署。提供个人版和服务器版，功能媲美Trello、Notion、Asana，支持多语言、团队协作、任务追踪管理。完全开源可控，Docker一键部署，适合个人和团队使用。"
canonical: https://www.passnat.com/posts/focalboard-open-source-self-hosted-project-management-deployment-guide-nas-internal-penetration-remote-access-tutorial
---

## 项目介绍

Focalboard 是一款开源、自托管的项目管理工具，致力于打造一个功能完整的替代方案，可媲美 Trello、Notion 和 Asana 等商业产品。它采用多语言支持，帮助个人和团队定义、组织、追踪和管理各类工作任务。

Focalboard 提供两个版本供用户选择：个人桌面版（支持 macOS、Windows 和 Linux）以及个人服务器版（支持多用户协作）。无论你是想要一个简洁的个人任务管理工具，还是需要一个团队级别的协作平台，Focalboard 都能满足需求。界面简洁直观，功能强大，完全开源，让你能够完全掌控自己的数据。

项目地址： [点我跳转](https://github.com/mattermost/focalboard)

## 部署安装

如果你需要在外网访问部署在家中或公司的 Focalboard 服务，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号以便备用。具体的详细操作步骤可以参考 [帕斯内网穿透全平台快速上手指南](https://www.passnat.com/posts/p)，帮助你轻松实现外网访问。

Focalboard 提供了开箱即用的 Docker 部署方案，以下是最快速的部署步骤：

### 使用 Docker 快速部署

打开你的 NAS 设备（飞牛 NAS、群晖等）或任何支持 Docker 的系统

进入 Docker 管理界面，找到 **本地镜像** 或 **镜像** 选项，选择 **添加镜像** - **从 URL 添加**

在镜像地址栏填入以下镜像地址：

```
mattermost/focalboard:latest
```

等待镜像下载完成后，点击 **启动** 或 **运行** 按钮启动容器

在容器配置界面进行以下设置：

**基础配置**

勾选 **开机自动启动**，确保服务器重启后容器能自动运行

**端口映射**

容器默认监听 **8000 端口**，你需要将其映射到宿主机。建议设置为：
容器内端口：`8000`
宿主机端口：`8000`（如果 8000 端口被占用，可修改为其他端口如 8080、9000 等，但容器内端口保持 8000 不变）

**存储配置**

Focalboard 需要持久化存储数据，建议在 NAS 上创建一个专用文件夹（例如 `/data/focalboard`），然后将其映射到容器内的 `/data` 目录，确保数据不会因容器重启而丢失。

**环境变量设置**

必须设置：
- `FOCALBOARD\_PORT`：服务器监听端口，默认值为 `8000`

可选设置（一般无需修改）：
- `FOCALBOARD\_DB\_TYPE`：数据库类型，默认值为 `sqlite`
- `FOCALBOARD\_DB\_PATH`：数据库文件路径，默认值为 `/data`

其他配置项保持默认即可，无需特殊修改。点击 **下一步** 完成容器启动。

**访问应用**

容器启动成功后，在浏览器中输入以下地址访问 Focalboard：

```
http://你的NAS地址:8000
```

例如：`http://192.168.1.100:8000`

至此，Focalboard 项目管理工具已成功部署并可正常使用。

本篇教程结束

---

原文：<https://www.passnat.com/posts/focalboard-open-source-self-hosted-project-management-deployment-guide-nas-internal-penetration-remote-access-tutorial>
