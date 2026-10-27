---
title: "基于Django框架的私人词典库系统，支持mdx和zim格式，局域网内轻松部署查询工具"
date: 2025-10-16
description: "django-mdict是基于Django框架的mdx和zim格式词典查询工具，提供局域网在线查询解决方案。支持多种词典格式，查询快速高效，界面简洁易用。可在家庭网络中部署私人词典库，配合内网穿透可远程访问。Docker快速部署，适合英文学习者、翻译工作者及知识爱好者使用。"
canonical: https://www.passnat.com/posts/private-dictionary-system-django-mdx-zim-local-network-deployment
---

## 项目介绍

django-mdict 是一款基于 Django 框架开发的词典查询工具，专为 mdx 和 zim 格式的词典文件量身打造。它提供了一个简洁而强大的局域网在线查询解决方案，让你能够轻松地在家庭网络中部署属于自己的私人词典库。

无论你是英文学习者、专业翻译工作者还是知识爱好者，django-mdict 都能帮助你快速查询和管理各类词典资源。项目支持多种词典格式，查询速度快，界面友好，是 GoldenDict、Mdict、欧陆词典等专业词典软件的完美补充。

项目地址：[点我跳转](https://github.com/jiangnianshun/django-mdict)

## 部署安装

想要在家庭网络外也能访问你的词典库？建议配置内网穿透工具，推荐使用 [帕斯内网穿透](https://passnat.com)。先注册账号备好，具体的详细操作指南可以 [点我查阅](https://www.passnat.com/posts/p)，帮助你快速掌握内网穿透的全套使用方法。

### Docker 快速部署

打开你的 NAS 设备或支持 Docker 的系统，进入本地镜像管理界面。

点击 **添加镜像** → **从 URL 添加**，在镜像地址栏填入：

```
jiangnianshun/django-mdict:latest
```

等待镜像下载完成后，点击启动按钮创建容器。

在容器配置界面中，需要进行以下设置：

**端口映射设置：**

将容器内部的 `18000` 端口映射到宿主机的 `18000` 端口（如果该端口已被占用，可修改宿主机端口号，但容器内部端口需保持为 `18000`）

**存储位置配置：**

创建一个文件夹用于存放你的词典库文件，例如 `/data/mdict\_library`，将其映射到容器内的 `/app/mdict\_library` 路径。如果你有发音文件库，也可创建 `/data/mdict\_audio` 文件夹并映射到 `/app/mdict\_audio`。

**环境变量设置：**

此项目无需特殊环境变量配置，保持默认即可。

勾选 **开机自动启动**，然后点击启动容器。

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:18000/mdict/
```

稍等片刻，词典查询界面就会加载完成。首次使用时，系统会引导你选择词典库的位置，按照提示操作即可。

## 部署完成

到此为止，你已经成功部署了属于自己的在线词典系统！现在你可以在局域网内的任何设备上随时随地查询词典，无需依赖互联网词典服务。配合内网穿透工具，即使身在外地也能轻松访问你的私人词典库。

祝你使用愉快！

---

原文：<https://www.passnat.com/posts/private-dictionary-system-django-mdx-zim-local-network-deployment>
