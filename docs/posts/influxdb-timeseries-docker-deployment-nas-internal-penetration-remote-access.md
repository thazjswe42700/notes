---
title: "InfluxDB时序数据库Docker部署指南，NAS内网穿透远程访问完整方案"
date: 2025-10-16
description: "InfluxDB是开源时序数据库，专为时间序列数据设计，提供高效存储和复杂分析能力。支持监控、物联网、金融等多种应用场景。本指南详解Docker快速部署方法，包括镜像配置、端口映射、存储设置及环境变量，助力企业级应用快速上线。"
canonical: https://www.passnat.com/posts/influxdb-timeseries-docker-deployment-nas-internal-penetration-remote-access
---

## InfluxDB 时序数据库部署指南

### 项目介绍

InfluxDB 是一个开源的时序数据库平台，专为处理时间序列数据而设计。它提供了强大的数据存储和查询接口，支持高效的数据写入和复杂的时序分析。无论是监控系统指标、物联网设备数据采集，还是金融数据分析，InfluxDB 都能轻松胜任。

该项目集成了完整的生态工具链，包括数据存储引擎、后台处理能力、用户管理系统、可视化仪表盘等功能，让你可以快速搭建一套完整的时序数据解决方案。项目采用 Go 语言开发，性能卓越，支持多租户架构，适合企业级应用部署。

项目地址：[点我跳转](https://github.com/influxdata/influxdb)

### 部署安装

如果你需要在外网环境中访问本地部署的 InfluxDB，建议配置内网穿透服务。这样可以让你随时随地安全地访问数据库。推荐使用 [帕斯内网穿透](https://passnat.com) 服务，先注册账号备用。具体的配置步骤可以参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p)。

#### 使用 Docker 快速部署

打开你的 NAS 设备或服务器的 Docker 管理界面。

点击'本地镜像'→'添加镜像'→'从 URL 添加'，在镜像地址栏输入：

```
influxdb:latest
```

等待镜像下载完成后，点击启动按钮创建容器。

**配置容器参数：**

勾选'开机自动启动'选项，确保服务重启后能自动运行。

**端口映射设置：**

将容器的 8086 端口映射到主机。这是 InfluxDB 的默认 API 服务端口。如果你的主机上该端口已被占用，可以修改主机端口号（比如改为 8087），但容器内部的 8086 端口保持不变即可。

**存储配置：**

InfluxDB 需要持久化存储数据。建议在 NAS 上创建一个专用文件夹（例如 /data/influxdb），然后将其映射到容器内的 /var/lib/influxdb2 目录，这样即使容器重启也不会丢失数据。

**环境变量设置：**

以下是可选的环境变量配置：

```
| 变量名 | 说明 | 默认值 | 是否必需 |
|--------|------|--------|----------|
| INFLUXDB_DB | 初始数据库名称 | - | 可选 |
| INFLUXDB_ADMIN_USER | 管理员用户名 | - | 可选 |
| INFLUXDB_ADMIN_PASSWORD | 管理员密码 | - | 可选 |
| INFLUXDB_HTTP_AUTH_ENABLED | 启用 HTTP 认证 | false | 可选 |
```

一般情况下，这些环境变量都不需要特别设置。容器启动后会进入初始化向导，你可以通过 Web 界面进行配置。

**启动访问：**

容器启动成功后，在浏览器中访问 http://你的服务器IP:8086，你会看到 InfluxDB 的初始化界面。按照提示创建用户账户、组织和数据桶，即可开始使用。

初始化完成后，你可以通过 Web UI 进行数据写入、查询和可视化操作。

本篇教程结束

---

原文：<https://www.passnat.com/posts/influxdb-timeseries-docker-deployment-nas-internal-penetration-remote-access>
