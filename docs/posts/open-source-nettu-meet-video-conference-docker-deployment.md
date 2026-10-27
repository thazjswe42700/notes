---
title: "开源私有视频会议系统Nettu Meet，Docker一键部署，支持屏幕共享和内网穿透远程访问"
date: 2025-10-16
description: "Nettu Meet是开源视频会议应用，专为在线教学和远程协作设计。支持实时音视频、屏幕分享、白板、聊天等功能。采用React和Node.js技术栈，可快速搭建私有会议系统。所有数据存储在自己服务器，完全掌控隐私。提供Docker一键部署方案，适合教育机构、企业内部使用。"
canonical: https://www.passnat.com/posts/open-source-nettu-meet-video-conference-docker-deployment
---

## 项目介绍

Nettu Meet 是一款开源的视频会议应用，专为在线教学和远程协作而精心设计。这是一个功能完整的云端会议解决方案，无需复杂配置，即可快速搭建属于自己的私有视频会议系统。

项目采用现代化技术栈构建，前端基于 React，后端使用 Node.js，支持实时音视频通话、共享白板、屏幕分享、在线聊天、文件上传等丰富功能。特别适合教育机构、培训机构或企业内部搭建私有的会议平台。

最棒的是，你可以完全掌控自己的数据，所有会议内容都存储在自己的服务器上，无需担心隐私泄露问题。

项目地址：[点我跳转](https://github.com/fmeringdal/nettu-meet)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透服务，推荐使用 [帕斯内网穿透](https://passnat.com)。先在官网注册账号备用，具体使用方法可参考 [内网穿透完全上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

### 使用 Docker 快速部署

打开你的 NAS 设备或服务器的 Docker 管理面板

点击 **本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：fmeringdal/nettu-meet:latest

等待镜像下载完成后，点击 **启动** 按钮启动容器

### 容器配置步骤

**勾选开机自启**：建议勾选此选项，确保服务器重启后应用自动运行

**端口映射配置**：

容器内部端口：5000（后端服务）

容器内部端口：3000（前端服务）

如果你的服务器上这些端口已被占用，可以修改宿主机端口号，但容器内的端口保持不变

**存储位置配置**：

建议创建以下目录用于数据持久化：

/data/nettu-meet/db - 用于存储数据库文件

/data/nettu-meet/redis - 用于 Redis 缓存数据

将这些目录分别映射到容器内对应位置

**环境变量设置**：

必须设置：

AUTHORIZATION\_SECRET：API 授权密钥（用于创建会议时的认证）

可选设置：

DATABASE\_URL：MongoDB 连接字符串（默认已配置）

REDIS\_URL：Redis 连接地址（默认已配置）

如无特殊需求，其他环境变量保持默认即可

### 启动完成

创建成功后，在浏览器中访问 http://你的服务器IP:3000

稍等片刻，即可看到 Nettu Meet 的登录界面，到此部署完成！

你现在可以创建会议室，邀请学生或同事加入，开始享受高效的在线教学或远程协作了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-nettu-meet-video-conference-docker-deployment>


## 相关文章

- [青蛇探针服务器监控系统，Docker一键部署实时可视化面板，支持远程SSH管理](qing-she-tan-zhen-fu-wu-qi-jian-kong-xi-tong-docker-yi-jian-bu-shu-shi-shi-ke-shi-hua-mian-ban-zhi-chi-yuan-cheng-ssh-guan-li.md)
- [高颜值React音乐播放器R3PLAYX，NAS极简部署，无社交纯听觉，支持网易云账号登录和实时歌词显示](r3playx-react-music-player-nas-deployment-netease-cloud-lyrics.md)
- [现代React推箱子游戏，500+谜题无限撤销，NAS极简部署内网穿透在线玩](modern-react-sokoban-game-500-puzzles-unlimited-undo-nas-simple-deployment-intranet-penetration-online-play.md)
- [聚合三大平台海量音乐，NAS 极简部署 Musicn 音乐库，一键启动在线播放下载](musicn-nas-online-music-player-aggregator.md)
- [自建相册管理系统Photoview，NAS极简部署内网穿透远程访问，摄影师RAW格式完美支持](photoview-nas---raw.md)
