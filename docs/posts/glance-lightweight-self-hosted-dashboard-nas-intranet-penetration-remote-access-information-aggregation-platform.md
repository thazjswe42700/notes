---
title: "轻量级自托管仪表板Glance，极简部署NAS内网穿透远程访问信息聚合平台"
date: 2025-10-16
description: "轻量级开源自托管仪表板应用，采用Go开发，资源占用极低。支持RSS、天气、股票、Docker等20余种小部件，可自由定制布局主题。二进制文件仅20MB，提供优雅简洁的信息聚合体验，适合个人和企业部署。"
canonical: https://www.passnat.com/posts/glance-lightweight-self-hosted-dashboard-nas-intranet-penetration-remote-access-information-aggregation-platform
---

## 项目介绍

Glance 是一款轻量级、高度可定制化的仪表板应用，它将您的各类信息流以优雅简洁的界面呈现出来。这是一个完全开源的自托管解决方案，专为那些希望掌控自己数据的用户而设计。

项目采用 Go 语言开发，具有极低的资源占用和快速的加载速度。Glance 支持丰富的小部件生态，包括 RSS 订阅、Reddit 帖子、黑客新闻、天气预报、YouTube 频道、Twitch 直播、股票行情、Docker 容器状态、服务器统计等多达二十余种内容源。您可以根据个人需求自由组合这些小部件，打造专属于自己的信息中心。

无论是在办公室的电脑上还是随身携带的手机上，Glance 都能为您提供一致的优雅体验。它支持多种主题切换、自定义布局、多页面标签管理，甚至允许您通过 CSS 进行深度定制。最重要的是，整个应用的二进制文件不到 20MB，Docker 镜像同样小巧高效。

项目地址：[点我跳转](https://github.com/glanceapp/glance)

## 部署安装

如果您想在外网也能访问这个仪表板，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，详细的使用指南请参考 [这篇教程](https://www.passnat.com/posts/p)，支持全平台快速上手。

### 使用 Docker 快速部署

这是最简便的方式，只需一条命令即可启动。打开飞牛 NAS 或其他支持 Docker 的 NAS 设备，进入本地镜像管理界面。

点击'添加镜像'，选择'从 URL 添加'，在镜像地址栏填入：

```
glanceapp/glance
```

等待镜像下载完成后，点击启动按钮开始创建容器。

### 端口配置

在容器启动配置中，您需要设置端口映射。默认配置为：

容器内部端口：`8080`

宿主机端口：`8080`（可根据需要修改）

如果您的 NAS 上 8080 端口已被占用，可以将宿主机端口改为其他值，比如 `8081`、`8082` 等，但容器内部端口必须保持为 `8080`。

### 存储配置

Glance 需要持久化存储配置文件。在存储位置设置中，请创建以下目录映射：

容器路径 `/app/config` 映射到宿主机的 `/volume1/glance/config`（或您 NAS 上任意合适的位置）

容器路径 `/app/assets` 映射到宿主机的 `/volume1/glance/assets`（用于存储自定义资源和样式）

### 环境变量

Glance 在基础使用中无需特殊的环境变量配置。所有的功能定制都通过 YAML 配置文件完成，无需额外的环保境设置。

### 启动完成

配置完成后点击启动。待容器成功运行，您可以在浏览器中访问：

```
http://您的NAS地址:8080
```

此时应能看到 Glance 的默认仪表板界面。

### Docker Compose 方式（推荐用于高级用户）

如果您对 Docker Compose 更加熟悉，可以使用以下方式部署。

首先，创建项目目录并下载完整的配置模板：

```
mkdir glance && cd glance && curl -sL https://github.com/glanceapp/docker-compose-template/archive/refs/heads/main.tar.gz | tar -xzf - --strip-components 2
```

这条命令会自动为您创建以下文件结构：

'docker-compose.yml' - Docker 编排配置

'config/home.yml' - 首页小部件配置

'config/glance.yml' - 全局设置和主题配置

'assets/user.css' - 自定义样式文件

'.env' - 环境变量文件

根据需要编辑这些文件后，运行：

```bash
docker compose up -d
```

如遇到问题，可通过以下命令查看日志：

```bash
docker compose logs
```

## 配置说明

Glance 的所有配置都通过 YAML 文件完成，无需数据库或复杂的后端设置。

主要配置文件：

'glance.yml' - 主配置文件，用于设置主题、添加多个页面/标签

'home.yml' - 首页的小部件布局和配置

'user.css' - 用于添加自定义 CSS 样式

常见小部件配置示例：

您可以配置 RSS 订阅源、Reddit 社区、天气信息、股票行情等。每个小部件都支持丰富的定制选项，比如缓存时长、显示数量限制等。

## 小结

Glance 以其轻量、高效、易定制的特点，成为自托管爱好者打造个人信息中心的理想选择。无论是监控服务器状态、聚合信息源，还是创建个性化仪表板，Glance 都能胜任。通过简单的 Docker 部署和灵活的 YAML 配置，您可以在几分钟内拥有一个属于自己的专业级仪表板。

本篇教程到此结束，祝您使用愉快！

---

原文：<https://www.passnat.com/posts/glance-lightweight-self-hosted-dashboard-nas-intranet-penetration-remote-access-information-aggregation-platform>
