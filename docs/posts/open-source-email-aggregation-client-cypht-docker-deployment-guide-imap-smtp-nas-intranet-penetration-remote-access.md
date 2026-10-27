---
title: "开源邮件聚合客户端Cypht Docker部署指南，支持IMAP/SMTP协议NAS内网穿透远程访问"
date: 2025-10-16
description: "Cypht是开源邮件聚合客户端，支持Gmail、Outlook等多种邮箱账户集成。采用模块化设计，支持IMAP/SMTP、JMAP和EWS协议，内置新闻阅读功能。本地聚合邮件，保护隐私安全。详细介绍Docker快速部署步骤、端口映射配置和存储设置。"
canonical: https://www.passnat.com/posts/open-source-email-aggregation-client-cypht-docker-deployment-guide-imap-smtp-nas-intranet-penetration-remote-access
---

## 项目介绍

Cypht 是一款功能强大的开源邮件聚合客户端，它将你所有的电子邮件账户整合在一个统一的界面中。无论你使用 Gmail、Outlook、还是其他任何支持 IMAP/SMTP 协议的邮箱，Cypht 都能将它们无缝融合在一起，让你不再需要在多个邮箱之间来回切换。

这款应用采用模块化设计架构，支持 IMAP/SMTP、JMAP 和 EWS 等多种邮件协议，同时还具备新闻阅读器的功能。Cypht 不会替代你现有的邮箱账户，而是在本地将它们聚合展示，保护你的隐私安全。界面简洁易用，功能完整，是追求高效邮件管理的用户的理想选择。

项目地址：[点我跳转](https://github.com/jasonmunro/cypht)

## 部署安装

如果你需要在外网访问 Cypht，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的配置步骤可以参考 [帕斯内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，轻松实现远程访问。

### 使用 Docker 快速部署

打开你的 NAS 设备或 Docker 管理界面，按照以下步骤操作：

**第一步：添加 Docker 镜像**

点击'本地镜像' - '添加镜像' - '从 URL 添加'，在镜像地址栏填入：

```
cypht/cypht:latest
```

等待镜像下载完成后，点击启动按钮。

**第二步：配置容器参数**

勾选'开机自动启动'后进入下一步。

**端口映射设置**

Cypht 默认运行在容器内的 80 端口（HTTP）和 443 端口（HTTPS）。你可以根据需要将其映射到宿主机的任意端口。例如：

容器端口 80 → 宿主机端口 8080

容器端口 443 → 宿主机端口 8443

如果遇到端口被占用的情况，只需修改宿主机端口即可，容器内部端口保持不变。

**存储位置配置**

Cypht 需要持久化存储用户配置和缓存数据。建议在 NAS 上创建一个专用文件夹（例如 `/mnt/data/cypht`），将容器内的 `/var/www/html` 目录映射到该位置，确保重启容器后数据不会丢失。

**环境变量设置**

Cypht 的基础部署不需要设置特殊的环境变量，所有配置都可以在首次启动后通过 Web 界面进行。直接点击下一步启动容器即可。

**第三步：访问应用**

容器启动成功后，在浏览器中输入：

```
http://你的NAS地址:8080
```

（根据你实际映射的端口号替换 8080）

首次访问会进入初始化设置页面，按照提示添加你的邮箱账户，Cypht 会自动检测邮件服务器设置。添加完成后，你就可以在统一的界面中管理所有邮箱了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-email-aggregation-client-cypht-docker-deployment-guide-imap-smtp-nas-intranet-penetration-remote-access>


## 相关文章

- [Rallly 约时间投票：NAS Docker 部署 + 内网穿透](rallly-nas-docker-group-scheduling-poll-intranet-penetration.md)
- [Ghost 博客：NAS Docker 部署 + 内网穿透，写完发个链接](ghost-docker-nas-one-click-cms.md)
- [本地部署AI大模型Ollama，离线运行Gemma Llama DeepSeek，支持内网穿透远程访问](local-deploy-ai-ollama-offline-gemma-llama-deepseek-intranet-tunneling.md)
- [开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问](appsmith-nas-docker-compose-local-deployment-guide.md)
- [开源AI工作空间AIaW：跨平台客户端，集成多家大模型，NAS本地部署内网穿透远程访问](aiaw-open-source-ai-workspace-cross-platform-client-multi-model-integration-nas-local-deployment-intranet-penetration-remote-access.md)
