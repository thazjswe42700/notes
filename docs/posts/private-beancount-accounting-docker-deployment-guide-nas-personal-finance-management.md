---
title: "私有Beancount记账系统Docker快速部署指南，NAS轻松搭建个人财务管理平台"
date: 2025-10-16
description: "Beancount-GS是一款基于Golang的开源复式记账系统，提供RESTful API接口和移动端前端。支持多账本管理、多币种交易、投资组合跟踪、账单自动导入等功能。可私有部署于NAS设备，轻松实现个人财务管理。支持Docker快速部署，完全掌控财务数据安全。"
canonical: https://www.passnat.com/posts/private-beancount-accounting-docker-deployment-guide-nas-personal-finance-management
---

## 项目介绍

Beancount-GS 是一个功能强大的复式记账系统，专为 Beancount 这一优秀的开源记账工具而设计。项目采用 Golang 开发后端服务，提供完整的 RESTful API 接口，并内置了适配移动端的前端页面，让你可以轻松实现私有部署的个人财务管理系统。

无论你是想管理日常开支、追踪投资收益，还是进行复杂的多币种账户管理，Beancount-GS 都能满足你的需求。它支持多账本管理、自动化账单导入、投资组合跟踪等高级功能，让财务管理变得简单高效。

[点我跳转](https://github.com/BaoXuebin/beancount-gs)

项目地址：

## 部署安装

如需在外网也能打开项目，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p)帕斯内网穿透全平台快速上手指南。

### 使用 Docker 快速部署

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
xdbin/beancount-gs:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

**端口映射设置：** 将容器内部的 **80** 端口映射到宿主机的 **10000** 端口（如果 10000 端口已被占用，可自行修改为其他端口号，但容器内端口保持 80 不变）。

**存储卷挂载：** 本项目需要挂载多个数据目录用于持久化存储，请创建以下文件夹并按照以下方式映射：

容器内路径

宿主机路径

说明

/data/beancount

/data/beancount

账本数据存储目录

/app/public/icons

/data/beancount/icons

图标资源目录

/app/config

/data/beancount/config

配置文件目录

/app/bak

/data/beancount/bak

备份文件目录

/app/logs

/data/beancount/logs

日志文件目录

**环境变量设置：** 此项目无需特殊环境变量配置，保持默认即可。

完成以上配置后点击启动，容器启动成功后即可通过浏览器访问 **http://NAS\_IP:10000** 来使用 Beancount-GS。

### 使用 Docker Compose 部署

如果你更倾向使用 Docker Compose 方式部署，在 NAS 上创建一个文件夹，然后创建 **docker-compose.yml** 文件，复制以下内容：

```yaml
version: '3.9'
services:
  app:
    container_name: beancount-gs
    image: xdbin/beancount-gs:${tag:-latest}
    ports:
      - '10000:80'
    volumes:
      - '${dataPath:-/data/beancount}:/data/beancount'
      - '${dataPath:-/data/beancount}/icons:/app/public/icons'
      - '${dataPath:-/data/beancount}/config:/app/config'
      - '${dataPath:-/data/beancount}/bak:/app/bak'
      - '${dataPath:-/data/beancount}/logs:/app/logs'
```

如果想自定义数据存储路径，在同一目录下创建 **var.env** 文件，填入以下内容：

```
tag=latest
dataPath=/你的自定义路径
```

然后执行命令启动：

```
docker-compose --env-file ./var.env up -d
```

启动完成后，同样通过 **http://NAS\_IP:10000** 访问应用。

## 功能特性

私有部署 - 完全掌控你的财务数据

多账本管理 - 支持同时管理多个独立账本

账户资产管理 - 灵活的账户体系和资产追踪

数据可视化 - 丰富的统计图表展示

多币种支持 - 轻松处理跨币种交易

标签分类 - 自定义标签快速分类记账

投资管理 - 内置 FIFO 算法支持投资组合跟踪

账单导入 - 支持支付宝、微信、工商银行、农业银行等第三方账单导入

分期记账 - 灵活的分期记账功能

事件管理 - 记录重要的财务事件

## 总结

Beancount-GS 为你提供了一个完整的私有财务管理解决方案，结合 Docker 的便捷性，即使是技术小白也能轻松部署使用。无论你是个人用户还是家庭财务管理者，都能在这个平台上找到适合自己的功能。现在就开始部署，开启你的智能财务管理之旅吧！

本篇教程结束

---

原文：<https://www.passnat.com/posts/private-beancount-accounting-docker-deployment-guide-nas-personal-finance-management>
