---
title: "轻量级客户端加密笔记本，NAS一键部署Notepad，口令登录随时随地访问笔记"
date: 2025-10-16
description: "Notepad是简洁高效的网页版笔记应用，采用AES-256客户端加密，支持口令登录和跨设备同步。基于Vue+Node.js构建，提供完整笔记管理功能。本文详细介绍Notepad的部署安装方法，包括NAS容器配置、端口映射、存储挂载等步骤，帮助用户快速搭建个人安全笔记本。"
canonical: https://www.passnat.com/posts/lightweight-encrypted-notes-nas-one-click-deploy-notepad-password-login
---

## 项目介绍

Notepad 是一个简洁高效的网页版笔记本应用，专为在线记录和安全存储笔记而设计。无需繁琐的注册流程，仅需一个口令短语即可登录使用。项目采用 Vue + Node.js + Express 技术栈构建，具有轻量级、高效率的特点。

最核心的特性是**完全的客户端加密**——你的所有笔记数据都在浏览器端进行加密处理，服务器无法读取任何内容。这意味着即使是应用的开发者也看不到你的笔记。笔记采用 AES-256 加密算法保护，安全性有保障。同时支持跨设备、跨服务器的笔记同步，让你随时随地访问自己的内容。

界面极简，功能完整，无需复杂配置，开箱即用。非常适合个人笔记、临时记录或团队间的文本共享。

项目地址：[点我跳转](https://github.com/Athlon1600/notepad)

## 部署安装

如果你需要在外网也能访问自己部署的 Notepad 应用，建议配置内网穿透服务。可以前往[帕斯内网穿透](https://passnat.com)注册账号，这样就能从任何地方安全地访问你的笔记本。详细的操作步骤可以参考[帕斯内网穿透全平台上手指南](https://www.passnat.com/posts/p)，流程简单易懂。

### 最快速的部署方式

打开你的飞牛 NAS、群晖 NAS 或其他 NAS 设备的管理后台。

点击**本地镜像**→**添加镜像**→**从 URL 添加**。

在镜像地址栏填入：athlon1600/notepad:latest

等待镜像下载完成，然后点击启动按钮创建容器。

### 容器配置步骤

1. 基础设置

勾选**开机自动启动**，这样 NAS 重启后应用会自动恢复运行。

2. 端口映射

端口设置这里需要注意：

容器内端口：3000（固定，不要改动）

宿主机端口：3000（可根据需要修改，如果 3000 端口已被占用，改成其他端口如 3001、3002 等）

如果你的 NAS 上 3000 端口已经被其他应用占用，可以修改宿主机端口为其他值，但容器内的 3000 端口保持不变。

3. 存储挂载

Notepad 需要持久化存储笔记数据，请在 NAS 上创建两个文件夹用于数据保存：

创建文件夹 '/backend/storage' 用于存储加密后的笔记文件

创建文件夹 '/backend/public' 用于存储前端资源

将这两个文件夹分别挂载到容器内对应的路径。这样即使容器重启，你的所有笔记也不会丢失。

4. 环境变量

环境变量设置这里，Notepad 已经有了合理的默认配置，你可以保持原样，无需修改。如果你有特殊需求，可以设置：

NODE\_ENV：运行环境，默认值为 'production'（生产环境）

其他配置项都不需要额外设置，直接保持默认即可。

5. 启动应用

完成上述配置后，点击下一步并确认启动容器。

创建成功后，你就可以通过浏览器访问 'http://你的NAS地址:3000' 来使用 Notepad 了。如果你修改了宿主机端口，记得把 3000 替换成你设置的端口号。

首次打开应用时，输入一个你想要的口令短语作为登录密码，然后就可以开始记录笔记了。这个口令短语会生成加密密钥，请妥善保管。

本篇教程结束

---

原文：<https://www.passnat.com/posts/lightweight-encrypted-notes-nas-one-click-deploy-notepad-password-login>


## 相关文章

- [端到端加密笔记分享，NAS 极简部署 Enclosed，零知识架构客户端加密，内网穿透外网访问](enclosed-end-to-end-encrypted-notes-nas-deployment-zero-knowledge-architecture.md)
- [极简命令行笔记本 Dnote 自建部署：Docker 快速上手，内网穿透远程访问笔记库](dnote-self-hosted-docker-deployment-internal-penetration-remote-access.md)
- [自托管私密笔记应用Notea，Docker极简部署，云存储灵活配置，内网穿透远程访问](self-hosted-private-notes-notea-docker-deployment-cloud-storage-remote-access.md)
- [轻量级Markdown笔记应用Note Mark，Docker极简部署NAS，支持内网穿透远程访问笔记](note-mark-markdown-docker-nas-intranet-penetration.md)
- [自托管笔记书签管理Benotes Docker部署指南，NAS轻松搭建私有知识库](self-hosted-notes-bookmarks-benotes-docker-nas-private-knowledge-base.md)
