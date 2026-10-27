---
title: "远程同步观影神器SyncTV，NAS一键部署，与全球好友实时同步看电影直播"
date: 2025-10-16
description: "SyncTV是开源远程同步观影应用，支持与全球朋友实时同步观看电影、直播和视频。提供视频同步播放、实时聊天、弹幕、WebRTC音视频通话等功能。兼容Alist、Bilibili、Emby等多种视频源，支持视频代理、直播代理和缓存加速。本文详解Docker快速部署方案，包括端口配置、存储映射和环境变量设置，让您轻松在NAS设备上搭建私人同步观影平台。"
canonical: https://www.passnat.com/posts/sync-tv-remote-synchronized-movie-watching-nas-one-click-deployment-real-time-global-friends
---

## 项目介绍

SyncTV 是一款开源的远程同步观影应用，让你可以与世界各地的朋友、家人一起在线观看电影、直播和视频。无论身在何处，都能实现完全同步的观看体验。

这款应用采用先进的同步技术，确保所有观众始终处于同一播放进度。你可以随意暂停、快进、快退、调整播放速度，其他人的屏幕会立即同步更新。SyncTV 不仅支持视频同步播放，还提供了实时聊天、弹幕、WebRTC 音视频通话等功能，让远程观影变成一场真正的社交体验。

项目支持多种视频源接入，包括 Alist、Bilibili、Emby 等，还提供了视频代理、直播代理和缓存加速等高级功能。无论你想观看本地视频、网络视频还是直播内容，SyncTV 都能轻松满足。

项目地址：[点我跳转](https://github.com/synctv-org/synctv)

## 部署安装

如需在外也打开项目需要配置内网穿透使用，点击前往 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的操作方法可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略。

### 最快速上手方案（Docker 单命令）

打开飞牛 NAS 或其他的 NAS 设备，进入 Docker 管理界面。

点击本地镜像 - 添加镜像 - 从 URL 添加。在镜像地址栏填入：**synctvorg/synctv**，等待镜像下载完成。

下载完毕后点击启动按钮启动容器。

**端口配置说明**

在容器启动配置中，需要进行端口映射设置：
容器内部端口：**8080**
宿主机端口：建议设置为 **8080**（如果该端口已被占用，可修改为其他端口如 **8081**、**8888** 等，但容器内端口保持 **8080** 不变）

**存储位置配置**

SyncTV 需要持久化存储用户数据和配置文件，请在存储位置设置中添加以下映射：
容器内路径：**/root/.synctv**
宿主机路径：建议创建 **/opt/synctv** 文件夹进行映射（可根据实际情况调整）

**环境变量设置**

SyncTV 的基础功能无需额外配置环境变量即可正常运行。如需启用用户注册功能，需要配置 OAuth2 服务（如 Google、Github 等），具体配置方法请参考官方文档。

**启动完成**

勾选开机自动启动后，点击启动。创建成功后可以通过浏览器访问 **http://你的NAS地址:8080** 来打开 SyncTV。

首次启动时，系统会自动创建一个超级管理员账户，默认用户名为 **root**，密码为 **root**。***请务必在首次登录后立即修改默认密码***，以确保系统安全。

本篇教程结束

---

原文：<https://www.passnat.com/posts/sync-tv-remote-synchronized-movie-watching-nas-one-click-deployment-real-time-global-friends>
