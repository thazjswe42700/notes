---
title: "高颜值React音乐播放器R3PLAYX，NAS极简部署，无社交纯听觉，支持网易云账号登录和实时歌词显示"
date: 2025-10-16
description: "R3PLAYX是一款基于React+Electron技术打造的高颜值第三方音乐播放器。支持网易云账号登录、私人FM、多音源播放、实时歌词显示和全局背景自定义。完全无社交功能，让你专注纯粹的音乐体验。支持Docker部署，可在NAS或服务器上搭建，轻松实现外网访问。简洁优雅的界面，高质量音乐享受。"
canonical: https://www.passnat.com/posts/r3playx-react-music-player-nas-deployment-netease-cloud-lyrics
---

## 项目介绍

R3PLAYX 是一款高颜值的第三方音乐播放器，采用 React + Electron 技术栈精心打造。它提供了一个简洁优雅的界面，让你能够享受高质量的音乐播放体验。无论是在线播放、MV 观看还是歌词显示，R3PLAYX 都为你准备好了。

这款播放器支持网易云账号登录（扫码、手机、邮箱等多种方式），内置私人 FM 功能，支持多音源播放，还能显示实时歌词和全局背景自定义。最重要的是，R3PLAYX 完全无社交功能，让你专注于音乐本身，享受纯粹的听觉盛宴。

项目地址：[点我跳转](https://github.com/Sherlockouo/music)

## 部署安装

想要在家里的 NAS 或其他设备上部署 R3PLAYX，让你随时随地都能访问自己的音乐库？如果需要在外网访问，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先完成账号注册。详细的操作步骤可以参考 [这份全平台上手攻略](https://www.passnat.com/posts/p)，帮助你快速搭建外网访问通道。

### Docker 部署（推荐）

打开你的 NAS 设备或服务器的 Docker 管理界面，按以下步骤进行：

**第一步：添加镜像**

点击'本地镜像' → '添加镜像' → '从 URL 添加'，在镜像地址栏填入：

```
sherlockouo/music:latest
```

等待镜像下载完成后，点击启动按钮。

**第二步：容器配置**

勾选'开机自动启动'选项，确保 NAS 重启后应用能自动运行。

**端口映射设置：**

容器内端口：`3000`

主机端口：`3000`（如果该端口已被占用，可修改为其他端口，如 `3001`、`3002` 等，但请记住你设置的端口号）

**存储位置（可选）：**

如需持久化保存播放记录和配置信息，建议在 NAS 上创建一个文件夹（如 `/data/r3playx`），然后将其映射到容器内的 `/app/data` 目录。

**环境变量设置：**

通常情况下无需特殊配置，保持默认即可。如有特殊需求，可根据项目文档进行调整。

**第三步：启动完成**

配置完成后点击'下一步'并启动容器。稍等片刻后，在浏览器中访问 `http://你的NAS地址:3000` 即可打开 R3PLAYX，开始享受音乐吧！

## 使用建议

首次使用需要用网易云账号登录，支持多种登录方式

在'设置'中可自定义全局背景和主题风格

右下角的 MV 按钮可播放歌曲 MV，右上角的歌词按钮可显示实时歌词

如遇到登录问题，请确保账号密码正确，扫码登录需在手机上确认

本篇教程结束

---

原文：<https://www.passnat.com/posts/r3playx-react-music-player-nas-deployment-netease-cloud-lyrics>


## 相关文章

- [现代React推箱子游戏，500+谜题无限撤销，NAS极简部署内网穿透在线玩](modern-react-sokoban-game-500-puzzles-unlimited-undo-nas-simple-deployment-intranet-penetration-online-play.md)
- [Win11React网页版Windows11桌面一键Docker部署，浏览器在线体验完整系统界面](win11-react-docker-browser-desktop.md)
- [自建相册管理系统Photoview，NAS极简部署内网穿透远程访问，摄影师RAW格式完美支持](photoview-nas---raw.md)
- [开源私有视频会议系统Nettu Meet，Docker一键部署，支持屏幕共享和内网穿透远程访问](open-source-nettu-meet-video-conference-docker-deployment.md)
- [开源日程管理系统Cal.com自建部署，完全控制数据NAS本地托管，支持内网穿透远程访问](open-source-calendar-management-cal-com-self-hosted-deployment-local-nas-storage-intranet-penetration-remote-access.md)
