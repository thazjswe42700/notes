---
title: "自托管联邦即时通讯Databag部署指南，树莓派轻量级私密通讯解决方案教程"
date: 2025-10-16
description: "Databag 是轻量级自托管联邦式即时通讯应用，支持端到端加密、音视频通话、群组讨论。本文详细介绍 Databag 在 NAS 容器中的快速部署方法，包括镜像配置、端口映射、数据持久化设置、管理员初始化、WebRTC 音视频配置等完整步骤，帮助用户轻松搭建私有通讯服务器。"
canonical: https://www.passnat.com/posts/self-hosted-databag-federated-messaging-deployment-guide-raspberry-pi-lightweight-private-communication
---

## 项目介绍

Databag 是一个专为自托管设计的联邦式即时通讯应用。它采用轻量级架构，消耗最少的服务器资源，甚至可以运行在树莓派 Zero 上。项目基于公钥-私钥身份验证机制，支持端到端加密通讯，即使是服务器管理员也无法查看加密的消息内容。

这是一个真正的去中心化通讯解决方案——不同节点上的账户可以相互通讯，支持音视频通话、群组讨论、无限参与者、消息加密存储等功能。界面简洁易用，支持 iOS、Android 和网页版，是搭建私有通讯网络的最佳选择。

项目地址：[点我跳转](https://github.com/balzack/databag)

## 部署安装

如果需要在外网访问你的 Databag 实例，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的跨平台部署指南可以 [点我查看](https://www.passnat.com/posts/p)。

### 最快速部署方案

打开飞牛 NAS 或其他 NAS 设备的容器管理界面。

点击'本地镜像' - '添加镜像' - '从 URL 添加'。

在镜像字段中填入：balzack/databag:latest，等待镜像下载完成后点击启动。

### 容器配置步骤

**基础设置：**

勾选'开机自动启动'，确保服务器重启后应用自动恢复

**端口映射：**

容器内端口：7000

主机端口：7000（如果该端口被占用，可修改主机端口号，但容器内端口必须保持为 7000）

**存储配置：**

在 NAS 上创建一个文件夹用于数据持久化（建议命名为 databag\_data）

将容器内路径 /var/lib/databag 映射到这个文件夹

这样即使容器重启，所有账户数据、消息记录都会被保留

**环境变量设置：**

**必须设置：**

ADMIN：管理员密码（自定义一个安全的密码）

其他配置项可保持默认，无需额外设置。

点击'下一步'完成部署。

### 初始化配置

容器启动成功后，在浏览器中访问 http://你的NAS地址:7000

点击右上角齿轮图标，输入你设置的管理员密码进入管理面板

在管理配置中设置'联邦主机'为你的域名（如果有的话），然后点击保存

点击用户图标生成新账户邀请链接，通过该链接创建账户

### 音视频通话配置（可选）

如果需要启用音视频通话功能，需要配置 STUN/TURN 中继服务器。在管理面板中设置：

启用 WebRTC 通话：打开开关

WebRTC 服务器地址：turn:34.210.172.114:3478?transport=udp（测试用）

WebRTC 用户名：user

WebRTC 密码：pass

生产环境建议部署自己的 coturn 服务器或使用 Cloudflare TURN 服务。

到这里，Databag 私有通讯服务器就已经完全部署好了。你现在拥有一个完全自主可控的通讯平台，可以邀请家人朋友加入，享受安全、隐私的即时通讯体验。

本篇教程结束

---

原文：<https://www.passnat.com/posts/self-hosted-databag-federated-messaging-deployment-guide-raspberry-pi-lightweight-private-communication>


## 相关文章

- [自建neko远程浏览器：飞牛NAS Docker部署+内网穿透外网访问](neko-remote-browser-nas-docker-deployment-intranet-penetration-external-access.md)
- [浏览器WebRTC视频通话应用Call-Me，NAS一键部署，支持屏幕共享与内网穿透远程通话](browser-webrtc-video-call-app-call-me-nas-one-click-deployment-screen-sharing-intranet-penetration-remote-calling.md)
- [远程同步观影神器SyncTV，NAS一键部署，与全球好友实时同步看电影直播](sync-tv-remote-synchronized-movie-watching-nas-one-click-deployment-real-time-global-friends.md)
- [浏览器P2P文件传输工具FastSend，WebRTC加密传输，NAS极简Docker部署，局域网极速秒传无限制](fastend-p2p-file-transfer-webrtc-nas-docker.md)
- [功能强大IP诊断工具MyIP Docker部署指南内网穿透远程访问配置](powerful-ip-diagnostic-tool-myip-docker-deployment-guide-intranet-penetration-remote-access-configuration.md)
