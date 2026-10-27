---
title: "个人知识库TiddlyWiki5 Docker极简部署NAS无公网内网穿透远程访问"
date: 2025-10-16
description: "TiddlyWiki5是功能强大的个人知识管理系统，由JavaScript编写，支持单HTML文件运行或Node.js服务器部署。高度可定制，整个UI用可编辑WikiText实现，适合笔记、知识库、博客和文档管理。完全独立，永久拥有使用权。本教程详解Docker部署步骤和NAS配置方法，助你快速搭建私人Wiki系统。"
canonical: https://www.passnat.com/posts/personal-knowledge-base-tiddlywiki5-docker-deployment-nas-intranet-tunneling-remote-access
---

## 项目介绍

TiddlyWiki5 是一个功能强大的个人知识管理系统，它是一个完整的、交互式的维基百科应用，完全由JavaScript编写。这个项目支持单HTML文件在浏览器中运行，也可以作为强大的Node.js服务器应用使用。

TiddlyWiki5的核心特色在于其高度的可定制性——整个用户界面本身就是用可编辑的WikiText实现的，这意味着你可以根据自己的需求随意修改和扩展。无论是个人笔记、知识库、博客还是文档管理，TiddlyWiki5都能胜任。最重要的是，它完全独立于任何公司，你可以永久拥有和使用它。

项目地址：[点我跳转](https://github.com/Jermolene/TiddlyWiki5)

## 部署安装

如果你想在外网也能随时访问你的个人知识库，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的跨平台配置指南可以 [点我查看](https://www.passnat.com/posts/p)。

### 最快速部署方式

打开你的NAS设备（飞牛NAS或其他支持Docker的NAS），进入Docker管理界面。

点击 **本地镜像** - **添加镜像** - **从URL添加**

在镜像地址栏填入：

```
tiddlywiki:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

### 容器配置说明

**端口映射设置：**

将容器内部的 *8080* 端口映射到你想要的外部端口（建议直接使用 *8080*）。如果你的NAS中 *8080* 端口已被占用，可以修改为其他端口，比如 *8888* 或 *9090*，但容器内部端口必须保持为 *8080*。

**存储位置配置：**

建议在NAS中创建一个专门的文件夹用于存储TiddlyWiki的数据，例如 */data/tiddlywiki*。将此路径映射到容器内的 */var/lib/tiddlywiki* 目录，这样你的所有笔记和配置都会被持久化保存。

**环境变量设置：**

TiddlyWiki5的Docker版本通常不需要特殊的环境变量配置，保持默认即可。如果你需要自定义监听端口或其他高级选项，可以参考官方文档进行配置。

勾选 **开机自动启动** 选项，这样NAS重启后TiddlyWiki会自动运行。

### 访问你的Wiki

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:8080
```

如果映射的是其他端口，将 *8080* 替换为你配置的端口号即可。

页面加载完成后，你就可以开始创建和编辑你的个人知识库了。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/personal-knowledge-base-tiddlywiki5-docker-deployment-nas-intranet-tunneling-remote-access>
