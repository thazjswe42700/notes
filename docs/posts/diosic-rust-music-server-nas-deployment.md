---
title: "开源Rust音乐服务器Diosic极简部署，NAS轻量级流媒体解决方案"
date: 2025-10-16
description: "Diosic是开源网络音乐服务器，采用Rust开发，内存占用极低性能卓越，支持Docker快速部署。提供简洁网页界面，轻松管理私人音乐库，随时随地流媒体播放音乐。适合NAS和硬件配置有限的服务器，开箱即用无需复杂配置，支持内网穿透远程访问。"
canonical: https://www.passnat.com/posts/diosic-rust-music-server-nas-deployment
---

## 项目介绍

Diosic 是一个开源的网络音乐服务器和流媒体播放器。采用 Rust 语言开发，具有极低的内存占用和卓越的性能表现，特别适合在硬件配置有限的服务器上部署。无论你是音乐爱好者还是家庭媒体库的管理者，Diosic 都能为你提供一个轻量级、高效的音乐流媒体解决方案。

通过简洁直观的网页界面，你可以随时随地访问和播放你的音乐收藏，还能对音乐库进行灵活的管理和控制。项目开箱即用，无需复杂配置，轻松搭建属于自己的私人音乐服务器。

unknown node[点我跳转](https://github.com/Reknij/diosic)

## 部署安装

unknown node[帕斯内网穿透](https://passnat.com)unknown node[全平台上手攻略](https://www.passnat.com/posts/p) 进行配置，轻松实现远程访问。

### 使用 Docker 快速部署

打开你的 NAS 设备或任何支持 Docker 的系统，进入 Docker 管理界面。

**第一步：添加镜像**

点击 `本地镜像` - `添加镜像` - `从 URL 添加`，在镜像地址栏填入：

```
reknij/diosic:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

**第二步：基础配置**

勾选 `开机自动启动` 选项，然后进行下一步配置。

**第三步：端口映射**

Diosic 默认运行在容器内的 `8080` 端口。你需要将其映射到宿主机的某个端口（比如 `8080`）。如果你的宿主机 8080 端口已被占用，可以修改为其他端口（如 `8888`、`9090` 等），但容器内部端口始终保持为 `8080` 不变。

端口映射配置示例：

容器内端口：`8080`

宿主机端口：`8080`（如有冲突可改为其他端口）

**第四步：存储配置**

Diosic 需要两个持久化存储目录：

音乐库目录 (`/library`）：用于存放你的音乐文件，建议在 NAS 上创建一个专门的文件夹（如 `/mnt/music`），然后映射到容器内的 `/library`

数据目录 (`/data`）：用于保存 Diosic 的配置和数据库文件，建议创建 `/mnt/diosic\_data` 文件夹并映射到容器内的 `/data`

**第五步：环境变量设置**

以下是可配置的环境变量：

**必须设置：**

LIB\_NAME\_1：你的音乐库名称（默认值：`My Library`）

**可选设置：**

PUBLIC\_URL：如果你需要通过特定的域名或公网地址访问，可以在这里设置（默认为空）

其他环境变量保持默认即可，无需修改。

**第六步：启动容器**

完成以上配置后，点击启动按钮。容器启动成功后，你可以通过以下地址访问 Diosic：

```
http://你的NAS_IP:8080
```

如果你修改了宿主机端口，请使用相应的端口号替换上面的 `8080`。

到此，Diosic 音乐服务器部署完成！你现在可以上传音乐文件到映射的音乐库目录，然后通过网页界面享受流媒体播放的便利。

---

本篇教程结束

---

原文：<https://www.passnat.com/posts/diosic-rust-music-server-nas-deployment>


## 相关文章

- [MeiliSearch闪电搜索引擎Docker极速部署，内网穿透远程访问一键搭建](meilisearch-docker-deployment-intranet-penetration-remote-access.md)
- [Oxker Docker容器管理工具TUI界面轻量级终端控制，Rust开发支持实时监控日志查看](oxker-docker-container-management-tui-lightweight-terminal-rust-realtime-monitoring.md)
- [自建联邦式博客引擎Plume NAS部署指南 内网穿透外网访问 Rust开源](plume-federated-blog-engine-nas-deployment-guide-intranet-penetration-external-access-rust-opensource.md)
- [轻量级文件服务器 DUFS 快速部署指南 NAS Docker 一键启用文件共享搜索上传](lightweight-file-server-dufs-quick-deployment-guide-nas-docker-one-click-enable-file-sharing-search-upload.md)
- [自建RustDesk远程桌面服务器,NAS极简部署无公网穿透远程控制,开源跨平台屏幕共享工具](rustdesk-nas-remote-desktop-server-self-hosted.md)
