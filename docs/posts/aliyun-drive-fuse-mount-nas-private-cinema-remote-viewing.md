---
title: "阿里云盘FUSE挂载工具,将云盘变身NAS私人影院,配合内网穿透远程在线观看"
date: 2025-10-16
description: "aliyundrive-fuse是阿里云盘本地磁盘挂载工具，通过FUSE技术将云盘直接挂载为本地磁盘，支持Docker一键部署。完美适配Emby、Jellyfin等影视播放器，让你的NAS/服务器轻松变身私人影视库。省去繁琐配置，享受流畅播放体验。"
canonical: https://www.passnat.com/posts/aliyun-drive-fuse-mount-nas-private-cinema-remote-viewing
---

## 项目介绍

**aliyundrive-fuse** 是一个专为阿里云盘用户量身打造的磁盘挂载工具。它通过 FUSE 技术将阿里云盘直接挂载为本地磁盘，让你可以像访问本地文件一样轻松访问云盘中的所有内容。这个项目特别适合与 Emby 或 Jellyfin 等影视播放器配合使用，让你的云盘瞬间变身为私人影视库。

相比其他需要多层中转的方案，aliyundrive-fuse 省去了繁琐的配置步骤，直接通过 FUSE 技术实现云盘挂载，让你的 NAS 或服务器能够直接读取阿里云盘中的影视内容，享受流畅的播放体验。

unknown node[点我跳转](https://github.com/messense/aliyundrive-fuse)

## 部署安装

unknown node[帕斯内网穿透](https://passnat.com)unknown node[点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手指南。

### Docker 一键部署

打开你的 NAS 设备或服务器的 Docker 管理界面，点击 **本地镜像** - **添加镜像** - **从 URL 添加**。

在镜像地址栏填入：

```
messense/aliyundrive-fuse:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

**配置容器参数：**

勾选 **开机自动启动** 后进入下一步。

**端口映射设置：**

这个项目主要用于挂载磁盘，无需外部端口映射。如果你需要通过网络访问，建议配合内网穿透使用。

**存储位置配置：**

创建一个本地文件夹用于存储挂载点和工作目录。比如在 NAS 上创建 /mnt/aliyundrive 文件夹，然后将其映射到容器内的 /mnt/aliyundrive 路径。

**环境变量设置：**

**必须设置：**

REFRESH\_TOKEN：你的阿里云盘刷新令牌（从阿里云盘官方获取）

**可选设置：**

READ\_BUFFER\_SIZE：读取缓冲区大小，默认为 10MB（10485760 字节），可根据网络情况调整

其他参数保持默认即可，直接下一步启动容器。

创建成功后，你的阿里云盘就会挂载到指定的本地路径。现在你可以在 Emby 或 Jellyfin 中添加这个挂载路径作为媒体库源，开始享受云盘中的海量影视内容了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/aliyun-drive-fuse-mount-nas-private-cinema-remote-viewing>
