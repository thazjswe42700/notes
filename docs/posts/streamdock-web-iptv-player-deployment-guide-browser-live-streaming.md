---
title: "网页版IPTV播放器Streamdock简易部署指南，无客户端浏览器直播观看体验"
date: 2025-10-16
description: "Streamdock是轻量级网页版IPTV播放器，无需安装客户端，浏览器即可观看电视直播。支持频道搜索、深色模式、相似推荐等功能。提供Docker快速部署，内置全球频道库，支持自定义M3U播放列表，画中画浮窗播放，适合体育赛事和国际电视频道观看。"
canonical: https://www.passnat.com/posts/streamdock-web-iptv-player-deployment-guide-browser-live-streaming
---

## 项目介绍

Streamdock 是一个轻量级的网页版 IPTV 播放器，专为喜欢在线观看电视直播的用户而设计。它采用现代化的网页技术构建，无需安装任何客户端，只需打开浏览器即可使用。项目提供了简洁直观的用户界面，支持频道搜索、深色模式自适应、相似频道推荐等实用功能。无论你是想观看体育赛事还是国际电视频道，Streamdock 都能为你提供便捷的解决方案。

项目地址：[点我跳转](https://github.com/limmer55/streamdock)

## 部署安装

如果你想在外网也能访问项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它支持全平台使用。具体操作可以参考 [这份上手指南](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### Docker 单命令部署（推荐）

这是最快速的部署方式，只需一条命令即可启动服务：

```bash
docker run -d --name streamdock --network host --restart unless-stopped ghcr.io/limmer55/streamdock:latest
```

执行完毕后，打开浏览器访问：

```
http://[你的设备IP]:6050/
```

就能看到 Streamdock 的界面了。首次使用时，如果没有设置 IPTV 播放列表，可以在设置页面中配置 M3U 播放列表的地址。

### Docker Compose 部署

如果你更习惯使用 Docker Compose，可以按以下步骤操作：

**第一步：创建配置文件**

在你的 NAS 或服务器上创建一个 'docker-compose.yml' 文件，复制以下内容：

```yaml
version: '3.8'
services:
  streamdock:
    image: ghcr.io/limmer55/streamdock:stable
    container_name: streamdock
    network_mode: host
    environment:
      M3U_URL: 'https://iptv-org.github.io/iptv/index.m3u'
    restart: unless-stopped
```

**第二步：启动服务**

在文件所在目录执行以下命令：

```
docker-compose up -d
```

**第三步：访问应用**

启动完成后，在浏览器中输入：

```
http://[你的设备IP]:6050/
```

**环境变量说明：**

M3U\_URL（可选）：IPTV 播放列表的地址。如果你有自己的 IPTV 源，可以在这里配置；如果没有设置，可以在应用的设置页面中后续添加。默认值为 iptv-org 提供的国际频道列表。

其他配置保持默认即可，无需额外修改。

## 功能特性

✨ 浏览器直接观看 - 无需安装任何客户端，任何设备的浏览器都能使用

🔍 智能搜索 - 快速查找你想看的电视频道

🌙 深色模式 - 自动根据系统设置切换亮暗主题，保护眼睛

📡 频道推荐 - 基于相似度为你推荐其他频道

🌍 多源支持 - 内置 iptv-org 的全球频道库，无需自备资源

📽️ 浮窗播放 - 支持画中画模式，边看边做其他事

## 温馨提示

项目目前仍在早期开发阶段，部分编码格式可能存在兼容性问题。Safari 浏览器暂不支持，建议使用 Chrome、Firefox 等现代浏览器获得最佳体验。

本篇教程结束，祝你使用愉快！🎉

---

原文：<https://www.passnat.com/posts/streamdock-web-iptv-player-deployment-guide-browser-live-streaming>
