---
title: "个性化首页面板Sun Panel，零代码可视化自建工作台，Docker快速部署，内网穿透远程访问"
date: 2025-10-16
description: "Sun Panel是一款界面简洁、功能强大的个性化首页面板应用。支持零代码可视化操作、多账户隔离、系统监控、自定义注入等功能。开箱即用，完美支持Docker部署和ARM架构。用户可通过简单拖拽操作快速搭建专属工作台，无需外部数据库。"
canonical: https://www.passnat.com/posts/personalized-homepage-panel-sun-panel-no-code-visual-workbench-docker-deployment-intranet-penetration-remote-access
---

## Sun Panel - 个性化首页面板，打造属于你的专属工作台

#### 项目介绍

Sun Panel 是一款界面简洁、功能强大、资源占用低的个性化首页面板应用。它采用现代化设计理念，提供零代码的可视化操作体验，让每个用户都能轻松打造属于自己的专属工作台。

这款应用支持多账户隔离、系统状态监控、自定义 JS/CSS 注入，内置丰富的 Iconify 图标库供自由组合使用。无需连接外部数据库，开箱即用，完美支持 Docker 部署，兼容 ARM 系统架构。无论你是技术爱好者还是普通用户，都能通过简单的拖拽操作快速搭建个性化的首页面板。

项目地址：[点我跳转](https://github.com/hslr-s/sun-panel)

#### 部署安装

如果你想在外网也能访问到自己部署的 Sun Panel，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号以备不时之需。具体的操作步骤可以参考 [内网穿透全平台快速上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

##### 使用 Docker 快速部署

打开你的 NAS 设备或服务器的 Docker 管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
sunpanel/sun-panel:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

##### 端口配置

在端口映射设置中，将容器内部的 **8888 端口**映射到主机的任意可用端口。如果 8888 端口已被占用，可以修改主机端口（例如改为 8889、9000 等），但容器内的 8888 端口保持不变即可。

##### 存储配置

Sun Panel 支持本地数据存储，建议创建一个持久化目录用于保存应用数据和配置。在存储位置设置中，将容器内的 */app/data* 目录映射到主机的一个文件夹（例如 */data/sun-panel*），这样即使容器重启也不会丢失数据。

##### 环境变量设置

Sun Panel 的基础功能无需配置特殊的环境变量，使用默认设置即可正常运行。如无特殊需求，可直接跳过环境变量配置步骤。

勾选 **开机自动启动** 选项后，点击下一步完成配置。

##### 访问应用

容器启动成功后，在浏览器中访问 *http://你的设备IP:8888*（如果修改了端口，请使用对应的端口号），即可打开 Sun Panel 的首页面板。

到此为止，Sun Panel 的部署就完成了！

本篇教程结束

---

原文：<https://www.passnat.com/posts/personalized-homepage-panel-sun-panel-no-code-visual-workbench-docker-deployment-intranet-penetration-remote-access>


## 相关文章

- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [开源AI工作流平台Langflow，可视化编辑器快速构建智能体，支持NAS本地部署与内网穿透远程访问](langflow-open-source-ai-workflow-platform-visual-editor-local-deployment.md)
- [个性化卡通头像生成器 Avataaars 一键部署，Docker 快速搭建内网穿透远程访问](personalized-cartoon-avatar-generator-avataaars-one-click-deployment-docker-quick-setup-intranet-penetration-remote-access.md)
- [自建个人导航页面服务器实时监控工具，Docker快速部署Jump内网穿透远程访问](personal-navigation-server-monitoring-docker-jump-intranet-penetration.md)
- [网页广播播放器PawTunes部署教程 Docker一键启动NAS内网穿透远程访问](pawtubes-docker-nas-remote-access-setup.md)
