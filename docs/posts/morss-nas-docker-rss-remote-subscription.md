---
title: "使用Morss完整抓取RSS全文内容，NAS Docker极速部署，内网穿透远程订阅"
date: 2025-10-16
description: "发现 RSS 源只有摘要？Morss 自动抓取完整文章内容，支持多格式输出。Docker 一键部署到 NAS，支持缓存加速、XPath 自定义规则等高级功能。完美适配各类 RSS 阅读器。"
canonical: https://www.passnat.com/posts/morss-nas-docker-rss-remote-subscription
---

## 项目介绍

Morss 是一个功能强大的 RSS 订阅增强工具，专门用于获取完整的 RSS 源内容。许多新闻网站的 RSS 订阅源只提供文章摘要，Morss 能够自动抓取完整的文章内容并重新打包成 RSS 源，让你在阅读器中直接获取全文。

项目支持多种输出格式（RSS、JSON、CSV、HTML），能够处理损坏的 RSS 源、跟踪链接去混淆、自定义 XPath 规则生成源等高级功能。无论是作为服务器运行、命令行工具使用，还是集成到 Python 项目中，Morss 都能完美胜任。

项目地址：[点我跳转](https://github.com/pictuga/morss)

## 部署安装

如果你需要在办公室或外出时访问部署的 Morss 服务，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的使用方法可以参考 [这篇完整指南](https://www.passnat.com/posts/p)，涵盖了全平台的快速上手步骤。

### 使用 Docker 快速部署

打开你的 NAS 设备或 Docker 管理界面。

**添加 Docker 镜像**

点击「本地镜像」→「添加镜像」→「从 URL 添加」。

在镜像 URL 栏填入：pictuga/morss:latest

等待镜像下载完成后，点击启动按钮。

**容器配置**

勾选「开机自动启动」，然后进行端口设置。

**端口映射设置**

容器内部运行在 8000 端口，你可以将其映射到主机的任意端口。如果主机的 8000 端口已被占用，可以改为其他端口（如 8001、8080 等），但容器内的端口保持 8000 不变。

示例配置：

容器端口：8000

主机端口：8000（或其他可用端口）

**存储配置**

Morss 支持缓存功能来加快源的加载速度。如果你希望缓存数据持久化保存，建议创建一个本地文件夹并映射到容器内的 /data 目录。

**环境变量设置**

以下是可选的环境变量配置：

环境变量 | 说明 | 默认值 | 是否必需
DEBUG | 启用调试模式，输出执行反馈 | 0 | 否
CACHE | 缓存后端选择（默认/redis/diskcache） | 默认内存缓存 | 否
CACHE\_SIZE | 缓存条目数限制 | 1000 | 否
TIMEOUT | HTTP 请求超时时间（秒） | 默认 | 否
MAX\_TIME | 最大文章抓取时间（秒） | 无限制 | 否
MAX\_ITEM | 最大抓取文章数 | 无限制 | 否
PORT | 服务运行端口 | 8000 | 否

如果你是初级用户，**无需设置任何环境变量**，直接使用默认配置即可。

**启动服务**

完成上述配置后，点击「下一步」启动容器。

**访问应用**

容器启动成功后，打开浏览器访问：http://你的NAS\_IP:8000

你会看到 Morss 的服务界面。现在你可以输入任何 RSS 源地址，Morss 会自动为你获取完整的文章内容。

## 使用示例

**在浏览器中使用**

访问格式：http://你的NAS\_IP:8000/:clip/RSS\_源地址

例如：http://192.168.1.100:8000/:clip/https://feeds.bbci.co.uk/news/rss.xml

:clip 参数会将完整文章内容附加到原始摘要下方，特别适合 Twitter 等内容聚合源。

**导入到 RSS 阅读器**

在你喜欢的 RSS 阅读器（如 Tiny Tiny RSS）中，将 Morss 处理后的源地址添加为订阅源，即可获得完整的文章内容。

本篇教程到此结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/morss-nas-docker-rss-remote-subscription>
