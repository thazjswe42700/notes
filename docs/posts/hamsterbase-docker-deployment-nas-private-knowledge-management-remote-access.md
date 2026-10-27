---
title: "本地知识库HamsterBase Docker部署指南，NAS私密收集网页资源，内网穿透远程访问知识管理系统"
date: 2025-10-16
description: "HamsterBase是本地优先、隐私至上的知识收集工具。支持网页保存、高亮批注、全文搜索等功能，采用键盘优先设计，与Logseq、Obsidian、Notion等工具无缝集成。所有数据完全本地存储，无需服务器，为你的个人知识库提供安全隐私保护。"
canonical: https://www.passnat.com/posts/hamsterbase-docker-deployment-nas-private-knowledge-management-remote-access
---

## 项目介绍

HamsterBase 是一款本地优先、隐私至上的知识收集工具。它专为离线使用而设计，无需依赖任何服务器，所有数据完全存储在本地，确保你的隐私不被侵犯。这是一个功能强大的网页保存和知识管理系统，支持网页高亮、批注、全文搜索等功能，让你能够轻松构建属于自己的个人知识库。

HamsterBase 采用键盘优先的设计理念，支持快捷键操作，让你可以用90%的功能仅通过键盘完成。同时它提供了与 Logseq、Obsidian、Notion、思源笔记等流行生产力工具的无缝集成，真正让数据为你所用。

项目地址：[点我跳转](https://hamsterbase.com/zh/)

## 部署安装

如果你想在外网也能访问本地部署的 HamsterBase，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的操作指南可以参考 [这份详细教程](https://www.passnat.com/posts/p)，轻松掌握全平台内网穿透的使用方法。

### Docker 快速部署

打开你的 NAS 设备（如飞牛 NAS 或其他支持 Docker 的设备），进入本地镜像管理界面。

点击 **添加镜像** - **从 URL 添加**，在镜像地址栏填入以下镜像地址：

```
hamsterbase/hamsterbase:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

容器配置步骤：

勾选开机自动启动，确保容器在 NAS 重启后自动运行

端口映射设置

容器内部运行在 3000 端口

你可以将其映射到主机的任意端口（如 3000、8080 等）

如果提示端口冲突，修改主机端口即可，容器内端口保持 3000 不变

存储位置配置

HamsterBase 会在 /app/data 目录下存储所有本地数据

建议在 NAS 上创建一个专用文件夹（如 /volume1/hamsterbase）

将容器的 /app/data 目录映射到这个文件夹，确保数据持久化保存

环境变量设置

HamsterBase 支持以下可选环境变量配置：

环境变量 | 说明 | 默认值

NODE\_ENV | 运行环境 | production

PORT | 服务端口 | 3000

一般情况下无需设置环境变量，保持默认配置即可。

完成部署

所有配置完成后，点击启动。容器成功运行后，你可以通过以下地址访问 HamsterBase：

```
http://你的NAS地址:3000
```

如果一切正常，你将看到 HamsterBase 的欢迎界面，随后可以开始使用这个强大的知识管理工具。

部署完成！

现在你已经拥有了一个完全本地化的知识收集系统。无需担心隐私泄露，所有数据都安全地存储在你的 NAS 中。开始保存、高亮和管理你感兴趣的网页内容吧！

---

原文：<https://www.passnat.com/posts/hamsterbase-docker-deployment-nas-private-knowledge-management-remote-access>
