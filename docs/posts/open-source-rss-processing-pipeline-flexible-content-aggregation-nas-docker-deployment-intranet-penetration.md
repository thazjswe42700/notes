---
title: "开源RSS处理管道系统，灵活转换聚合内容，NAS Docker快速部署，内网穿透远程访问"
date: 2025-10-16
description: "RSS Funnel 是强大的 RSS 处理管道系统，支持全文抓取、内容过滤、关键词高亮、源合并、格式转换等功能。集成 Web UI，单个二进制文件即可运行。本教程介绍 Docker 快速部署方法，包括镜像添加、端口配置、存储挂载和环境变量设置等步骤，帮助用户在 NAS 上轻松部署专属 RSS 信息流处理工具。"
canonical: https://www.passnat.com/posts/open-source-rss-processing-pipeline-flexible-content-aggregation-nas-docker-deployment-intranet-penetration
---

## 项目介绍

RSS Funnel 是一个强大的 RSS 处理管道系统，能够以多种方式对 RSS/Atom 源进行灵活处理和转换。这是一个开箱即用的工具，集成了Web UI，无需额外依赖，一个单独的二进制文件即可运行。

项目支持丰富的功能特性：自动抓取全文内容、从HTML页面生成RSS源、移除不需要的元素、按关键词过滤文章、高亮标记关键词、拆分聚合内容为单独文章、合并多个源、格式转换、执行自定义JavaScript转换、应用图片代理等多项功能。

无论你是RSS重度用户还是内容聚合爱好者，RSS Funnel 都能帮你打造专属的信息流处理工具。

项目地址：[点我跳转](https://github.com/shouya/rss-funnel)

## 部署安装

如果需要在外网也能访问该项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即便在办公室或出门在外，也能随时随地访问家中的RSS处理服务。具体配置方法可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速入门指南。

### 使用 Docker 快速部署

打开你的飞牛 NAS 或其他 NAS 设备

点击 **本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：

```
ghcr.io/shouya/rss-funnel:latest
```

等待镜像下载完成后，点击 **启动** 按钮启动容器

勾选 **开机自动启动** 后进入下一步

端口配置**：这里需要设置容器的端口映射**

主机端口可以根据你的需求自定义，比如 '4080'

容器内部端口固定为 '4080'

如果提示端口被占用，只需修改主机端口号即可，容器内端口保持不变

存储配置**：需要创建一个文件夹用于存放配置文件**

在 NAS 上创建一个新文件夹，比如 '/mnt/rss-funnel'

将其挂载到容器内的 '/funnel.yaml' 路径（用于存放RSS处理规则配置文件）

环境变量设置**：**

必须设置：

'RSS\_FUNNEL\_CONFIG'：配置文件路径，填写 '/funnel.yaml'

'RSS\_FUNNEL\_BIND'：服务绑定地址，填写 '0.0.0.0:4080'

其他变量无需额外配置，保持默认即可

点击下一步完成启动

创建成功后，即可通过 'http://你的NAS\_IP:4080' 访问 RSS Funnel 的Web界面

到这里，RSS Funnel 就已经成功部署完成了！你可以开始配置你的RSS源处理规则，享受高效的内容聚合体验。

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-rss-processing-pipeline-flexible-content-aggregation-nas-docker-deployment-intranet-penetration>


## 相关文章

- [自建全文RSS提取服务 无需跳转看完整文章 Docker一键部署NAS内网穿透远程访问](full-text-rss-extraction-service-docker-nas-intranet-penetration.md)
- [自建 RSS 阅读器 FreshRSS：飞牛 NAS Docker 部署，关注的更新汇到一页，配内网穿透在外面也能刷](self-hosted-rss-reader-freshrss-nas-docker-deployment-intranet-penetration.md)
- [使用Morss完整抓取RSS全文内容，NAS Docker极速部署，内网穿透远程订阅](morss-nas-docker-rss-remote-subscription.md)
- [轻量级自托管仪表板Glance，极简部署NAS内网穿透远程访问信息聚合平台](glance-lightweight-self-hosted-dashboard-nas-intranet-penetration-remote-access-information-aggregation-platform.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
