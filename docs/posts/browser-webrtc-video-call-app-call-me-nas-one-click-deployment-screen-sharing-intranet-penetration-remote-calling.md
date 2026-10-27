---
title: "浏览器WebRTC视频通话应用Call-Me，NAS一键部署，支持屏幕共享与内网穿透远程通话"
date: 2025-10-16
description: "Call-Me是基于WebRTC的浏览器视频通话应用，支持一对一高清实时通话。无需复杂配置，用户名登录即可快速发起视频通话。提供摄像头切换、实时文字聊天、屏幕共享等功能，支持Docker一键部署，完整REST API便于集成。"
canonical: https://www.passnat.com/posts/browser-webrtc-video-call-app-call-me-nas-one-click-deployment-screen-sharing-intranet-penetration-remote-calling
---

### 项目介绍

Call-Me 是一个基于 WebRTC 技术的浏览器视频通话应用，让你无需任何复杂配置就能进行一对一的高清视频通话。无论是在家里、办公室还是任何有网络的地方，都能轻松与朋友、家人或同事进行实时视频交流。

项目采用 Node.js 开发，支持多种部署方式。你可以直接使用用户名登录，选择要通话的对方，一键发起视频通话。项目还提供了丰富的功能特性：支持摄像头、麦克风、扬声器的无缝切换，实时文字聊天，视频隐藏、音视频切换、屏幕共享等功能应有尽有。同时，项目提供了完整的 REST API，方便集成到你的网站或应用中。

[点我跳转](https://github.com/miroslavpejic85/call-me)

项目地址：

### 部署安装

如果你想在外网也能使用这个应用，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) 来实现，先注册好账号以备后用。详细的操作步骤可以参考 [这份完整指南](https://www.passnat.com/posts/p)，涵盖了全平台的使用方法。

## Docker 一键部署（推荐）

打开你的飞牛 NAS、群晖 NAS 或其他支持 Docker 的 NAS 设备。

进入 本地镜像 → 添加镜像 → 从 URL 添加

镜像地址填写：mirotalk/cme:latest

等待镜像下载完成后，点击启动按钮启动容器。

### 容器配置

**基础配置：**

勾选 开机自动启动，这样 NAS 重启后应用会自动运行

点击下一步进入端口设置

**端口映射设置：**

容器内端口为 8000，主机端口也建议设置为 8000。如果你的 NAS 上 8000 端口已被占用，可以修改主机端口（比如改成 8001），但容器内端口 8000 保持不变。

协议 | 主机端口 | 容器端口
---|---|---
TCP | 8000 | 8000

**存储位置：**

这个应用不需要持久化存储，所以可以不配置额外的卷挂载。

**环境变量设置（可选）：**

以下是常用的可配置环境变量，使用默认值即可正常运行：

环境变量 | 说明 | 默认值
---|---|---
PORT | 应用运行端口 | 8000
NODE\_ENV | 运行环境 | development
HOST\_PASSWORD\_ENABLED | 是否启用主机密码保护 | false
HOST\_PASSWORD | 主机密码（启用时需设置） | 123456789
STUN\_SERVER\_ENABLED | 是否启用 STUN 服务器 | true
API\_KEY\_SECRET | API 密钥 | call\_me\_api\_key\_secret

**重要提示：** 大多数情况下，使用默认配置就能正常工作，无需修改任何环境变量。如果你需要启用密码保护或修改 API 密钥，再进行相应的设置。

设置完成后点击下一步，启动容器。

### 访问应用

容器启动成功后，打开浏览器访问：

```
http://你的NAS地址:8000
```

例如：http://192.168.1.100:8000

进入应用后：

1. 使用任意用户名登录

2. 邀请其他人使用不同的用户名登录

3. 在用户列表中选择要通话的对方，点击通话按钮即可发起视频通话

### 功能特性速览

✨ 一对一视频通话 - 基于 WebRTC 的高清实时通话

🎥 灵活的媒体控制 - 随时切换摄像头、麦克风、扬声器

💬 实时文字聊天 - 通话中可进行文字交流

🖥️ 屏幕共享 - 支持屏幕共享功能

🔗 快速分享 - 生成通话链接，一键邀请对方

📱 跨平台支持 - 支持各种浏览器和设备

🔌 REST API - 完整的 API 接口，方便集成

本篇教程结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/browser-webrtc-video-call-app-call-me-nas-one-click-deployment-screen-sharing-intranet-penetration-remote-calling>


## 相关文章

- [私密日记极简部署，纯文本记录无数据库，Hibiscus.txt Docker内网穿透随处访问](hibiscus-txt-private-diary-docker-deployment.md)
- [自托管短链接服务Snapp，私密数据掌控无忧，NAS极简部署零烦恼，内网穿透远程访问](self-hosted-url-shortener-snapp-private-data-nas-deployment-intranet-penetration.md)
- [自建图片社区平台Szurubooru，Docker极简部署NAS，内网穿透远程访问图片库](szurubooru-docker-nas-deployment-intranet-penetration-remote-access-image-library.md)
- [本地部署AI大模型Ollama，离线运行Gemma Llama DeepSeek，支持内网穿透远程访问](local-deploy-ai-ollama-offline-gemma-llama-deepseek-intranet-tunneling.md)
- [Markopolis自托管Markdown笔记发布平台，完全开源无限制，NAS极简部署即可用，支持内网穿透远程访问](markopolis-self-hosted-markdown-notes-publishing-platform-open-source-nas-deployment.md)
