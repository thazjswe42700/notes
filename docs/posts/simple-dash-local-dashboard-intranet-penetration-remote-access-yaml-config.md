---
title: "极简网页仪表盘SimpleDash，本地快速部署，内网穿透远程访问，YAML配置自定义链接管理"
date: 2025-10-16
description: "SimpleDash是一款基于Flask框架的极简主义网页仪表盘应用，提供分类链接管理、本地应用快捷方式和Google日历集成功能。采用深色主题设计，支持YAML配置自定义，响应式界面适配各种设备。无论创建个人导航页面还是应用启动器，SimpleDash都能完美胜任。"
canonical: https://www.passnat.com/posts/simple-dash-local-dashboard-intranet-penetration-remote-access-yaml-config
---

## 项目介绍

SimpleDash 是一款极简主义的网页仪表盘应用，灵感源自 FLAME Dashboard 但做了大幅简化。它基于 Flask 框架构建，提供了一个清爽、高效的个人导航和应用管理平台。

这款应用具有分类链接管理、本地应用快捷方式、Google 日历集成等实用功能，采用深色主题设计搭配透明元素，界面简洁优雅。无论你是想创建一个个人导航页面，还是想要一个本地应用启动器，SimpleDash 都能完美胜任。支持 YAML 配置文件自定义，响应式设计适配各种设备，让你随时随地快速访问常用资源。

[点我跳转](https://github.com/securemindorg/SimpleDash)

## 部署安装

如果需要在外网也能打开项目，建议配置内网穿透工具以便远程访问。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能轻松实现远程访问本地仪表盘。具体操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台上手指南。

### Docker 单命令快速部署

打开你的 NAS 设备或本地 Docker 环境，执行以下命令即可快速启动 SimpleDash：

**Windows 用户：**

```bash
docker run -d -p 5000:5000 -v "%APPDATA%/SimpleDash:/app/data/static" securemindorg/simpledash:latest
```

**Linux/Mac 用户：**

```bash
docker run -d -p 5000:5000 -v "$HOME/.simpledash:/app/data/static" securemindorg/simpledash:latest
```

就这么简单！Docker 会自动下载镜像并启动容器。

### 端口配置说明

上面命令中的 -p 5000:5000 表示将容器内部的 5000 端口映射到你的主机 5000 端口。如果你的主机 5000 端口已被占用，可以修改前面的数字来改变映射端口，例如 -p 8080:5000 表示通过 8080 端口访问应用。

### 数据持久化

-v 参数用于挂载存储目录。这样你的配置文件和背景图片等数据就能被保存下来，即使重启容器也不会丢失。上面的命令已经为你配置好了合适的挂载路径。

### 环境变量设置

SimpleDash 采用 YAML 配置文件方式管理设置，无需特殊的环境变量配置。容器启动后会自动初始化必要的目录结构。

### 访问应用

容器启动成功后，打开浏览器访问 http://你的IP:5000（如果修改了端口则替换为相应端口号）即可看到 SimpleDash 仪表盘。

### 配置自定义

首次启动后，你可以在挂载的数据目录中找到 links.yaml 文件，通过编辑这个文件来自定义你的链接分类、应用快捷方式和 Google 日历集成。同时还可以在 uploads 目录中放置你喜欢的背景图片。

本篇教程结束

---

原文：<https://www.passnat.com/posts/simple-dash-local-dashboard-intranet-penetration-remote-access-yaml-config>
