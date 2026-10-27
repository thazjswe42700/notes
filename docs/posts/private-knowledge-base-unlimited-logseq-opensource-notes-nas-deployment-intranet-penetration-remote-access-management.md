---
title: "私人知识库无限制 Logseq 开源笔记 NAS 部署 内网穿透远程访问管理"
date: 2025-10-16
description: "Logseq是注重隐私保护的开源知识管理平台，支持Markdown和Org-mode格式。提供强大的知识管理、PDF批注、任务管理功能，拥有丰富的插件和主题生态。支持多平台移动应用，帮助学生和专业人士高效管理想法、笔记和知识库，随时随地提升生产力。"
canonical: https://www.passnat.com/posts/private-knowledge-base-unlimited-logseq-opensource-notes-nas-deployment-intranet-penetration-remote-access-management
---

## 项目介绍

**Logseq** 是一个注重隐私保护的开源知识管理与协作平台。它采用现代化的技术架构构建，支持多种文件格式（Markdown 和 Org-mode），提供强大的知识管理、PDF 批注、任务管理等功能。

无论你是学生、专业人士，还是任何希望以清晰有序的方式管理想法和笔记的人，Logseq 都能帮助你提升生产力，优化工作流程。该项目还拥有不断扩展的插件和主题生态，支持多平台移动应用，让你能够随时随地访问知识库。

项目地址：[点我跳转](https://github.com/logseq/logseq)

## 部署安装

想要在外网也能打开你的 Logseq 知识库？推荐使用 [帕斯内网穿透](https://passnat.com) 来实现远程访问。先在官网注册账号备用，然后参考 [这份详细教程](https://www.passnat.com/posts/p) 了解如何在各平台快速上手内网穿透功能。

### Docker 单命令快速启动

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
logseq/logseq:latest
```

等待镜像下载完成后，点击 **启动** 按钮启动容器。

### 容器配置说明

**勾选开机自动启动** 后进入下一步。

**端口映射设置：**

容器内端口：`80`（Logseq Web 服务运行端口）

主机端口：`3000`（可根据需要修改，如果 3000 端口已被占用，可改为其他端口，但请记住你设置的端口号）

**存储位置配置：**

Logseq 需要持久化存储你的知识库数据。建议在 NAS 上创建一个专用文件夹（例如 `/mnt/logseq-data`），然后将其映射到容器内的 `/root/.logseq` 目录，以确保你的笔记和数据不会因容器重启而丢失。

**环境变量设置：**

此项目无需特殊环境变量配置，保持默认即可。直接点击 **下一步** 启动容器。

### 访问你的 Logseq

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:3000
```

稍等片刻，Logseq 的界面就会加载完成，你可以开始创建和管理你的知识库了！

本篇教程结束。祝你使用愉快！🎉

---

原文：<https://www.passnat.com/posts/private-knowledge-base-unlimited-logseq-opensource-notes-nas-deployment-intranet-penetration-remote-access-management>
