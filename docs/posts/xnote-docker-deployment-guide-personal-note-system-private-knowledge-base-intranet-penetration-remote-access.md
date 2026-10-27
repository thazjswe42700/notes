---
title: "Xnote个人笔记系统Docker部署指南，自建私密知识库随处可得内网穿透远程访问"
date: 2025-10-16
description: "Xnote是轻量级个人笔记系统，支持Markdown、表格、相册等多种格式。采用Python开发，完全跨平台，Docker快速部署。提供笔记管理、文件管理、工具箱等功能，100%数据控制权，支持插件扩展，是搭建私人知识库的理想选择。"
canonical: https://www.passnat.com/posts/xnote-docker-deployment-guide-personal-note-system-private-knowledge-base-intranet-penetration-remote-access
---

Xnote 个人笔记系统 - Docker 部署完全指南

## 项目介绍

Xnote 是一款专为个人打造的轻量级笔记系统，它以简洁高效著称，集多维度数据管理功能于一身。这是一个真正属于你的知识库平台——支持 Markdown、表格、相册、列表等多种笔记格式，让你可以灵活地组织和管理所有的知识资料。

项目采用 Python 技术栈构建，完全跨平台支持（Windows、Mac、Linux），无论你是技术爱好者还是普通用户，都能轻松上手。最重要的是，你拥有 100% 的数据控制权，所有信息都掌握在自己手中，支持多种数据库环境运行，可以在云端部署也能本地运行。

Xnote 不仅提供了笔记管理、文件管理、工具箱等核心功能，还支持插件扩展机制，让你可以根据实际需求开发定制化功能。无论是记录日常笔记、管理项目文档，还是搭建个人知识库，Xnote 都能满足你的需求。

项目地址：[点我跳转](https://github.com/xupingmao/xnote)

## 部署安装

如果你想在外网也能访问自己的笔记系统，建议配置内网穿透工具。可以前往 [帕斯内网穿透](https://passnat.com)

注册账号，这样即使在办公室或出差在外，也能随时随地访问你的私人笔记库。详细的配置步骤可以 [点击这里查看](https://www.passnat.com/posts/p)

，平台提供了全平台的上手攻略，非常友好。

## Docker 快速部署

Xnote 提供了非常便捷的 Docker 部署方式，我们推荐使用 Docker Compose 来部署，这样能更好地管理数据持久化和配置文件。

## 部署步骤

打开你的 NAS 设备或服务器的管理面板（飞牛 NAS、群晖、或其他 NAS 系统）。

## 第一步：准备持久化存储

首先需要创建一个数据目录用于存放笔记数据和配置文件，这样即使容器重启，你的数据也不会丢失。

```
mkdir data
```

## 第二步：复制并配置文件

将默认配置文件复制到本地进行自定义配置：

```
cp ./config/boot/boot.default.properties ./boot.local.properties
```

根据你的需求编辑 'boot.local.properties' 文件，可以修改端口、数据库连接等参数。

## 第三步：使用 Docker Compose 启动

复制下列的配置文件，新建 'docker-compose.yml' 文件：

```yaml
version: '3'
services:
  server:
    build:
      context: .
    container_name: xnote
    volumes:
      - ./data:/home/data
      - ./boot.local.properties:/home/config/boot/boot.sae.properties
    ports:
      - 1234:1234
```

然后执行启动命令：

```
docker-compose up -d
```

## 第四步：端口配置说明

上面的配置中，'1234:1234' 表示将容器内的 1234 端口映射到宿主机的 1234 端口。如果你的宿主机上 1234 端口已被占用，可以修改前面的数字，比如改成 '8888:1234'，这样就能通过 'http://你的IP:8888' 访问 Xnote 了。容器内部的端口（后面的 1234）保持不变即可。

## 第五步：存储位置说明

配置中的两个 'volumes' 挂载点说明如下：

./data:/home/data - 笔记数据存储目录，所有你创建的笔记、上传的文件都会保存在这里

./boot.local.properties:/home/config/boot/boot.sae.properties - 配置文件挂载

建议在启动前先创建好 'data' 目录，确保权限正确。

## 第六步：启动并访问

容器启动成功后，打开浏览器访问：

```
http://你的服务器IP:1234/
```

初始账号为 'admin'，默认密码为 '123456'。首次登录后，强烈建议修改密码以保护你的数据安全。

## 功能特性速览

多格式笔记 - 支持 Markdown、表格、相册、列表等多种格式

智能组织 - 通过笔记本、标签、置顶等方式灵活管理文档

文件管理 - 完整的文件上传下载、编辑、搜索功能，支持超大文件

工具箱 - 内置文本处理、编解码、二维码生成等实用工具

插件扩展 - 支持自定义插件，满足个性化需求

完整备份 - 自动保存笔记修改历史，数据安全有保障

## 部署完成

至此，你已经成功部署了自己的 Xnote 个人笔记系统！现在你可以开始记录和管理你的知识了。如果在使用过程中遇到任何问题，可以在 [GitHub 项目主页](https://github.com/xupingmao/xnote)

提交 Issue 或评论。

祝你使用愉快！

---

原文：<https://www.passnat.com/posts/xnote-docker-deployment-guide-personal-note-system-private-knowledge-base-intranet-penetration-remote-access>
