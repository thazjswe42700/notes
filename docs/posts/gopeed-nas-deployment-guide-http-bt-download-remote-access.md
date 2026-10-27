---
title: "Gopeed高速下载器NAS部署指南，跨平台支持HTTP与BT下载，内网穿透远程访问"
date: 2025-10-16
description: "Gopeed是一款由Golang和Flutter打造的跨平台高速下载器，支持HTTP、BitTorrent、Magnet等多种协议。提供Windows、macOS、Linux、Android、iOS全平台版本，具有高度可定制化功能、API集成、浏览器扩展等特性。界面简洁易用，支持Docker一键部署，是全能下载利器的完美选择。"
canonical: https://www.passnat.com/posts/gopeed-nas-deployment-guide-http-bt-download-remote-access
---

## Gopeed - 全能下载利器，跨平台高速下载器

### 项目介绍

Gopeed（全称 Go Speed）是一款由 Golang 和 Flutter 精心打造的高性能下载器，支持 HTTP、BitTorrent、Magnet 等多种协议，真正做到了全平台覆盖。无论你是 Windows、macOS、Linux 用户，还是手机党，都能找到适配版本。

这不仅仅是一个简单的下载工具，Gopeed 还是一个高度可定制化的下载平台。通过集成 API 或安装开发扩展，你可以实现更多强大的功能，让下载变得更加智能和高效。界面简洁美观，操作直观易上手，即使是技术小白也能快速掌握。

项目地址：[点我跳转](https://github.com/GopeedLab/gopeed)

### 部署安装

如果你想在外网也能访问 Gopeed，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号保存备用。具体的配置步骤可以参考 [这份详细攻略](https://www.passnat.com/posts/p)，涵盖了全平台的使用方法，新手也能轻松上手。

#### 最快速上手方案

Gopeed 提供了开箱即用的 Docker 部署方案，只需一条命令就能快速启动：

```bash
docker run -d \
  --name gopeed \
  -p 9999:9999 \
  -e PUID=0 \
  -e PGID=0 \
  -e UMASK=022 \
  -v ~/gopeed/Downloads:/app/Downloads \
  --restart unless-stopped \
  liwei2633/gopeed
```

操作步骤如下：

打开你的 NAS 设备（飞牛 NAS、群晖或其他支持 Docker 的 NAS），进入 Docker 管理界面。

点击「本地镜像」-「添加镜像」-「从 URL 添加」，在镜像地址栏填入：

```
liwei2633/gopeed
```

等待镜像下载完成，然后点击启动按钮。

勾选「开机自动启动」确保容器随系统启动，然后进入下一步。

**端口配置：**

在端口设置中，需要配置以下映射：
主机端口 → 容器端口：9999 → 9999（HTTP 访问端口）
如果你的 NAS 上 9999 端口已被占用，可以修改主机端口（冒号前面的数字），比如改成 8888:9999，这样就能通过 http://ip:8888 访问。容器内的端口保持不变即可。

**存储配置：**

Gopeed 需要一个目录来存放下载文件。建议在 NAS 上创建一个专用文件夹（如 /mnt/gopeed/Downloads），然后在存储挂载部分配置：
容器路径：/app/Downloads
主机路径：你创建的下载文件夹路径
如果需要持久化保存应用配置和历史记录，可以额外挂载：
容器路径：/app/storage
主机路径：~/gopeed/storage

**环境变量设置：**

以下环境变量已在命令中配置，通常无需修改：

环境变量 | 说明 | 默认值
PUID | 进程用户 ID | 0
PGID | 进程用户组 ID | 0
UMASK | 文件权限掩码 | 022

如果需要添加身份验证（可选），可以在启动命令中追加：

```
-e USERNAME=yourname -e PASSWORD=yourpassword
```

其他配置保持默认即可，直接启动容器。

**访问应用：**

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:9999
```

稍等片刻，Gopeed 的下载管理界面就会完整加载出来，你可以立即开始使用。

### 功能亮点

多协议支持：HTTP、BitTorrent、Magnet 链接一站式下载
跨平台覆盖：Windows、macOS、Linux、Android、iOS 全支持
高度可定制：支持 API 集成和扩展开发，功能无限延伸
浏览器集成：提供浏览器扩展，接管浏览器下载任务
简洁易用：界面友好，操作直观，新手秒上手

本篇教程结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/gopeed-nas-deployment-guide-http-bt-download-remote-access>


## 相关文章

- [RetroArch复古游戏模拟器前端，支持NES、SNES、PS等多平台，NAS Docker极速部署，内网穿透远程畅玩经典游戏。](retroarch-nes-snes-ps-nas-docker-emulator.md)
- [私有部署开源聊天应用VoceChat，NAS轻松搭建安全私密聊天系统，内网穿透远程访问](vocechat-private-deployment-nas-internal-network-penetration.md)
- [免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库](lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access.md)
- [Standard Notes 私密笔记自建 - 端到端加密开源方案，NAS 极简部署指南](standard-notes-private-notes-self-hosted-end-to-end-encryption-open-source-nas-simple-deployment-guide.md)
- [阿里云盘CLI命令行工具NAS部署指南，本地文件云端备份同步方案详解](aliyun-pan-cli-nas-deployment-guide-local-cloud-backup-sync-solution.md)
