---
title: "自建私人音乐库随时随地听，Go轻量级音乐服务器Euterpe，NAS一键部署内网穿透"
date: 2025-10-16
description: "自托管音乐流媒体服务Euterpe，采用Go语言开发，支持MP3、FLAC、OGG等多种格式。提供简洁Web界面，自动下载专辑封面，可通过浏览器随时访问个人音乐库。支持Docker快速部署，内置搜索、批量下载、用户认证等功能，是搭建私人音乐服务器的理想选择。"
canonical: https://www.passnat.com/posts/go-lightweight-music-server-euterpe-nas-one-click-deployment-intranet-penetration
---

## 项目介绍

Euterpe 是一款功能强大的自托管音乐流媒体服务，让你可以随时随地畅享自己的音乐库。相比传统的网络挂载方式，Euterpe 提供了一个优雅的解决方案——只需一个浏览器就能访问你的音乐收藏，就像拥有自己的私人 Spotify 一样。

这是一个轻量级的音乐服务器，采用 Go 语言开发，打包为单一二进制文件，无需复杂的依赖配置。它支持 MP3、FLAC、OGG、WAV、M4A 等多种常见音频格式，提供了简洁美观的 Web 界面，还能自动下载专辑封面和艺术家图片。无论你在家还是在外，都能通过浏览器轻松访问和播放你的音乐。

[点我跳转](https://github.com/ironsmile/euterpe)

项目地址：

## 部署安装

若要在外网也能访问你的音乐库，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以参考 [这份详细攻略](https://www.passnat.com/posts/p)，一步步轻松上手。

### 使用 Docker 快速部署

打开你的 NAS 设备（飞牛 NAS 或其他品牌均可）。

在本地镜像管理中，选择「添加镜像」→「从 URL 添加」，输入以下镜像地址：

```
ironsmile/euterpe:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

勾选「开机自动启动」确保服务持久运行，然后进入下一步。

**端口设置**

容器内部运行在 9996 端口。建议将其映射到主机的 8080 端口（或其他未被占用的端口），这样你就能通过 http://你的IP:8080 访问音乐服务。如果遇到端口冲突，可以自由修改宿主机端口，但容器内端口需保持为 9996。

**存储位置配置**

你需要创建一个文件夹用来存放音乐库。建议在 NAS 上创建一个专用目录（如 /mnt/music），然后将其映射到容器内的 /root/Music 路径。这样 Euterpe 就能扫描并播放这个目录中的所有音乐文件。

**环境变量设置**

此项目的 Docker 镜像无需特殊的环境变量配置，所有设置都会在首次启动时自动生成。如有高级需求，可后续编辑配置文件进行调整。

点击启动，容器创建成功后，打开浏览器访问 http://你的NAS\_IP:8080，即可看到 Euterpe 的音乐播放界面。

到这里，部署就完成了！

### 功能特性速览

简洁高效：单一二进制文件，响应速度快

格式支持丰富：MP3、FLAC、OGG、WAV、M4A、Opus 等

智能封面管理：自动从 Cover Art Archive 下载专辑封面

艺术家信息：从 Discogs 自动获取艺术家图片

强大搜索：支持按歌曲名、艺术家、专辑搜索

批量下载：一键下载整张专辑为 ZIP 文件

用户认证：支持 HTTP Basic、Token 等多种认证方式

开放 API：提供 RESTful API，支持第三方客户端接入

跨平台客户端：iOS/Android 移动应用、GTK 桌面客户端等

本篇教程结束

---

原文：<https://www.passnat.com/posts/go-lightweight-music-server-euterpe-nas-one-click-deployment-intranet-penetration>


## 相关文章

- [在线音乐标签编辑器 Web版 支持FLAC MP3 M4A 远程NAS音乐管理 内网穿透](online-music-tag-editor-web-flac-mp3-m4a-nas-intranet-penetration.md)
- [个人音乐流媒体服务器Airsonic-Advanced部署教程，NAS内网穿透远程访问音乐库](airsonic-advanced-nas-deployment-guide-remote-music-streaming.md)
- [聚合三大平台海量音乐，NAS 极简部署 Musicn 音乐库，一键启动在线播放下载](musicn-nas-online-music-player-aggregator.md)
- [免费在线虚拟钢琴演奏平台 AutoPiano NAS 极简部署 内网穿透远程弹奏](free-online-virtual-piano-autopiano-nas-simple-deployment-intranet-penetration-remote-playing.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
