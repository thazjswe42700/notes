---
title: "开源客服工单系统 Zammad 快速部署指南 NAS 内网穿透远程访问"
date: 2025-10-16
description: "Zammad是开源客服工单管理系统，集邮件、聊天、电话、社交媒体多渠道于一体。提供简洁易用的界面、强大的工单管理、自动化流程和团队协作功能，显著提升客户服务效率。支持Docker快速部署，适合各类规模企业。"
canonical: https://www.passnat.com/posts/open-source-ticketing-system-zammad-quick-deployment-guide-nas-intranet-penetration-remote-access
---

## Zammad - 开源客服工单管理系统部署指南

### 项目介绍

Zammad 是一个功能强大的开源客服工单管理平台，采用 GNU AGPLv3 开源协议。它是一个基于网页的综合性客户支持系统，能够整合来自邮件、在线聊天、电话和社交媒体等多个渠道的客户沟通。

无论您是在为数量众多的客户问询而忙碌，还是团队沟通协调存在困难，亦或是被繁琐的工单管理工作所困扰，Zammad 都能为您提供一站式的解决方案。它不仅界面简洁易用，而且具备强大的工单管理、自动化流程、团队协作等功能，让您可以更高效地处理客户服务工作，显著提升团队的工作效率。

项目地址：[点我跳转](https://github.com/zammad/zammad)

### 部署安装

如果您需要在外网也能访问到 Zammad，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号以备不时之需。具体的详细操作指南可以 [点我查看](https://www.passnat.com/posts/p)，里面有全平台的快速上手教程。

#### Docker 部署（推荐）

打开您的 NAS 设备或者装有 Docker 的服务器，进入本地镜像管理界面，选择从 URL 添加镜像。

镜像地址填写：zammad/zammad-docker-compose:latest

等待镜像下载完成后，点击启动按钮启动容器。

**勾选开机自动启动**后进入下一步。

**端口配置说明**：

Zammad 默认使用 **8080 端口**进行访问。如果您的系统提示该端口已被占用，可以修改宿主机端口号，但容器内部端口请保持为 8080。例如：

宿主机端口：8888

容器内端口：8080

这样配置后，您就可以通过 http://您的IP:8888 来访问 Zammad。

**存储挂载配置**：

Zammad 需要持久化存储来保存工单数据、用户信息等重要数据。建议在 NAS 上创建一个专门的文件夹（例如 /data/zammad），然后将其映射到容器内的 /opt/zammad 目录。这样即使容器重启，所有数据也不会丢失。

**环境变量设置**：

必须设置：

RAILS\_ENV: 运行环境（默认值：production）

POSTGRESQL\_DB: PostgreSQL 数据库名（默认值：zammad）

POSTGRESQL\_USER: 数据库用户名（默认值：zammad）

POSTGRESQL\_PASS: 数据库密码（建议修改为强密码）

可选设置（一般无需修改）：

ELASTICSEARCH\_ENABLED: 是否启用 Elasticsearch 搜索引擎（默认值：true）

MEMCACHED\_NODES: 缓存节点配置（默认值：memcached:11211）

其他配置项保持默认即可，无需额外修改。

**系统要求**：

请确保您的服务器至少有 **4GB 的 RAM** 可用，以便 Zammad 和 Elasticsearch 正常运行。

点击启动后，系统会自动初始化数据库和相关服务。首次启动可能需要 2-5 分钟，请耐心等待。

#### 访问应用

部署完成后，打开浏览器访问 http://您的IP:8080（如果修改过端口，请使用对应的端口号）。

首次访问时，系统会引导您完成初始化设置，包括创建管理员账号、配置基本参数等。按照提示逐步完成即可。

### 总结

通过以上步骤，您已经成功部署了 Zammad 客服工单管理系统。现在您可以开始创建工单、管理客户沟通、配置工作流程等操作。如果遇到任何问题，建议查阅 [Zammad 官方文档](https://docs.zammad.org) 获取更多帮助。

祝您使用愉快！

---

原文：<https://www.passnat.com/posts/open-source-ticketing-system-zammad-quick-deployment-guide-nas-intranet-penetration-remote-access>
