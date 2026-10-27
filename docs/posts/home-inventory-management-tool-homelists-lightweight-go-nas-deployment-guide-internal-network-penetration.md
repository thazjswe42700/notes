---
title: "家庭物品库存管理工具 HomeLists，轻量级 Go 应用 NAS 部署指南及内网穿透配置"
date: 2025-10-16
description: "HomeLists是一款轻量级家庭物品库存管理工具，基于Go语言开发。帮助你轻松追踪冰箱食材、日用消耗品等家中物品，提供直观的仪表板界面和多种主题选择。支持Docker快速部署到NAS设备，配置简便，数据安全存储。"
canonical: https://www.passnat.com/posts/home-inventory-management-tool-homelists-lightweight-go-nas-deployment-guide-internal-network-penetration
---

## HomeLists - 家庭物品管理助手

### 项目介绍

HomeLists 是一款简洁高效的家庭物品库存管理工具。它帮助你轻松追踪家里的消耗品库存，比如冰箱里的食材、家中的日用品等。通过直观的仪表板界面，你可以一目了然地查看所有物品的库存状态，再也不用担心忘记家里还有什么了。

这个项目采用轻量级设计，基于 Go 语言开发，界面简洁美观，提供多种主题选择。无论是追踪食材、日用品还是其他消耗品，HomeLists 都能帮你管理得井井有条。

[点我跳转](https://github.com/aceberg/HomeLists)

项目地址：[点我跳转](https://github.com/aceberg/HomeLists)

### 部署安装

如果你需要在外网也能访问这个应用，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号。具体的操作步骤可以参考 [帕斯内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，让你随时随地都能访问家里的物品管理系统。

#### Docker 快速部署

打开你的 NAS 设备（飞牛 NAS 或其他支持 Docker 的 NAS），点击本地镜像-添加镜像-从 URL 添加，进行以下操作：

**镜像地址填写：** `aceberg/homelists:latest`

等待镜像下载完成后，点击启动按钮启动容器。

**配置容器参数：**

勾选开机自动启动后进入下一步。

**端口映射设置：**

容器端口：`8842`

主机端口：`8842`（如果你的 NAS 上 8842 端口已被占用，可以修改主机端口为其他端口，比如 8843、8844 等，但容器内的端口保持 8842 不变）

**存储位置配置：**

需要创建一个文件夹用于存储数据库和配置文件。建议在 NAS 上创建 `/data/homelists` 目录，然后将其映射到容器内的 `/data/homelists` 路径。这样你的所有物品列表和数据都会被妥善保存。

**环境变量设置：**

**必须设置：**

`TZ`：你所在的时区，例如 `Asia/Shanghai`（中国）、`Asia/Tokyo`（日本）等，确保时间显示正确

**可选设置：**

`GUIPORT`：Web 界面端口，默认值 `8842`（通常无需修改）

`THEME`：界面主题，默认值 `superhero`（可选择其他 Bootstrap 主题，如 `darkly`、`lumen` 等）

`DBPATH`：数据库路径，默认值 `/data/homelists/sqlite.db`（通常无需修改）

其他参数无需设置，直接下一步启动容器即可。

**访问应用：**

容器启动成功后，在浏览器中访问 `http://你的NAS\_IP:8842` 即可打开 HomeLists 物品管理界面，开始记录和追踪你家里的物品库存。

本篇教程结束

---

原文：<https://www.passnat.com/posts/home-inventory-management-tool-homelists-lightweight-go-nas-deployment-guide-internal-network-penetration>
