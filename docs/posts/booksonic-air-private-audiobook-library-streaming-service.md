---
title: "自建私人有声书库，无限音频随处听，Booksonic Air 极简部署流媒体服务"
date: 2025-10-16
description: "Booksonic Air是开源个人有声书流媒体服务器，支持MP3、AAC、OGG等多种音频格式，可管理数百GB音频库。完全免费开源，支持Windows、Mac、Linux全平台，提供官方Android应用和Subsonic API兼容。Docker快速部署，自动调整比特率适配各种网络环境，让你随处畅享个人有声书收藏。"
canonical: https://www.passnat.com/posts/booksonic-air-private-audiobook-library-streaming-service
---

## Booksonic Air - 随处畅享你的有声书库

### 项目介绍

Booksonic Air 是一款开源的个人有声书流媒体服务器，让你能够随时随地访问自己收藏的有声书资源。这是一个基于 Airsonic 构建的现代化音频流媒体解决方案，专为有声书爱好者量身定制。

项目采用 Java 技术构建，支持超大规模音频库管理（可达数百GB），完美兼容 MP3、AAC、OGG 等多种音频格式。通过内置的转码插件，它还能实时转换 WMA、FLAC、APE、Musepack、WavPack 和 Shorten 等多种音频格式。无论你的网络带宽如何，Booksonic Air 都能自动调整比特率以适配你的连接速度，让你在任何地方都能流畅收听。

该项目完全开源，采用 GNU General Public License v3 许可证，不含任何许可密钥或限制，是真正的自由软件。无论是 Windows、Mac、Linux 还是其他 Unix 变体，Booksonic Air 都能完美运行。同时，项目还提供了官方 Android 应用，以及对 Subsonic API 的完整支持，兼容众多第三方客户端。

项目地址：[点我跳转](https://github.com/popeen/Booksonic-Air)

### 部署安装

如果你需要在外网也能访问你的有声书库，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即便在办公室或旅途中也能随时随地打开你的个人音频库。具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台快速上手指南。

#### Docker 快速部署

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入以下内容：

```
popeen/booksonic-air:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

#### 端口映射设置

在容器配置中，需要进行以下端口映射：

容器端口 `8080` 映射到主机端口（建议使用 `8080` 或其他未被占用的端口）

如果提示端口已被占用，你可以修改主机端口号，但容器内部端口需保持为 `8080` 不变。

#### 存储位置配置

Booksonic Air 需要持久化存储你的有声书文件和配置数据。建议在 NAS 上创建以下目录进行映射：

/music - 用于存放你的有声书音频文件

/var/booksonic - 用于存储应用配置和数据库文件

在 NAS 中创建这些文件夹后，将其分别映射到容器对应的路径。

#### 环境变量设置

Booksonic Air 的基础配置相对简单，通常无需特殊的环境变量设置。如果你有特殊需求，可以参考以下可选环境变量：

`JAVA\_OPTS` - Java 运行参数（高级用户使用）

一般情况下保持默认即可，直接下一步启动容器。

#### 访问应用

创建成功后，在浏览器中访问：

```
http://你的NAS地址:8080
```

首次打开时会看到 Booksonic Air 的初始化界面。按照提示完成基础配置，包括设置管理员账号密码、添加音乐库路径等。配置完成后就可以开始上传和管理你的有声书了。

### 部署完成

到此为止，你已经成功部署了 Booksonic Air。现在你可以将自己收藏的有声书文件放入配置的音乐目录中，然后通过 Web 界面或官方 Android 应用随时随地享受你的有声书库。祝你收听愉快！

---

原文：<https://www.passnat.com/posts/booksonic-air-private-audiobook-library-streaming-service>


## 相关文章

- [Standard Notes 私密笔记自建 - 端到端加密开源方案，NAS 极简部署指南](standard-notes-private-notes-self-hosted-end-to-end-encryption-open-source-nas-simple-deployment-guide.md)
- [免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库](lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access.md)
- [个人音乐流媒体服务器Airsonic-Advanced部署教程，NAS内网穿透远程访问音乐库](airsonic-advanced-nas-deployment-guide-remote-music-streaming.md)
- [开源AI工作空间AIaW：跨平台客户端，集成多家大模型，NAS本地部署内网穿透远程访问](aiaw-open-source-ai-workspace-cross-platform-client-multi-model-integration-nas-local-deployment-intranet-penetration-remote-access.md)
- [WeekToDo开源周计划管理应用，极简任务管理，本地部署隐私保护，Docker一键启动NAS自建。](week-todo-open-source-task-management-local-deployment-docker-nas.md)
