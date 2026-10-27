---
title: "NextBeats复古Lofi音乐播放器部署指南，Docker一键启动，YouTube集成播放，音效混合定制，NAS内网穿透远程访问"
date: 2025-10-16
description: "NextBeats是一款基于Next.js和TypeScript构建的现代Lofi音乐播放器，具有复古电视机风格界面。支持YouTube集成、环境音效混合、主题自定义和频道管理功能。本文详细介绍Docker快速部署步骤、容器配置说明及功能体验，帮助用户在NAS等环境中轻松搭建个人音乐播放解决方案。"
canonical: https://www.passnat.com/posts/nextbeats-retro-lofi-music-player-docker-deployment-guide-youtube-integration-sound-mixing-nas-remote-access
---

NextBeats - 复古风格Lofi音乐播放器部署指南

## 项目介绍

NextBeats 是一款现代化的、高度可定制的 Lofi 音乐播放器，采用 Next.js 和 TypeScript 构建。这是一个专为编程、学习或放松而设计的音乐播放解决方案，拥有精美的复古电视机风格界面，让你在工作学习间隙享受舒适的音乐氛围。

项目支持 YouTube 集成播放、丰富的环境音效混合、主题自定义、频道管理等功能。你可以添加自己喜爱的 Lofi 流媒体资源，混合咖啡厅、打字机、壁炉、雨声等多种环保音效，营造专属于你的完美工作或学习环境。界面响应式设计确保在各类设备上都能获得良好体验。

项目地址：[点我跳转](https://github.com/btahir/next-beats)

## 部署安装

想要在公网或外出时也能访问你的 NextBeats 播放器？建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，详细的配置步骤可以 [参考这份攻略](https://www.passnat.com/posts/p)，帮助你轻松实现远程访问。

### 使用 Docker 快速启动

打开你的 NAS 设备（飞牛 NAS、群晖或其他支持 Docker 的系统）

点击进入 **本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏中填入：node:20-alpine（基础镜像，用于构建环境）

等待镜像下载完成后，使用以下命令启动 NextBeats 容器：

```bash
docker run -p 3000:3000 next-beats
```

如果你希望一步到位，也可以直接构建并运行：

```bash
docker build -t next-beats .
docker run -p 3000:3000 next-beats
```

### 容器配置说明

#### 端口映射

容器内部运行端口：3000

主机访问端口：3000

如果你的主机上 3000 端口已被占用，可以修改映射配置。例如，若想通过 8080 端口访问，可使用：

```bash
docker run -p 8080:3000 next-beats
```

然后通过 http://你的IP:8080 访问应用。

#### 存储与持久化

NextBeats 使用浏览器本地存储（localStorage）保存你的自定义频道、音效设置和主题偏好，这些数据会自动持久化，无需额外配置存储目录。

#### 环境变量设置

NextBeats 无需特殊的环境变量配置，开箱即用。所有个性化设置都通过界面操作完成，自动保存在本地。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS地址:3000
```

即可看到 NextBeats 的复古电视机风格界面。现在你可以开始添加自己喜爱的 Lofi 频道、混合各种环保音效，打造专属的音乐氛围了！

## 功能体验

频道切换：轻松切换不同的 Lofi 音乐流
音效混合：叠加咖啡厅、打字机、壁炉、雨声、白噪音、风声等环保音效
独立音量控制：音乐和音效各自拥有独立的音量调节
自定义频道：添加任何 YouTube Lofi 流媒体 URL 作为自己的频道
主题定制：根据个人喜好切换不同的视觉主题

## 总结

通过以上步骤，你就能在 NAS 或其他 Docker 环境中快速部署 NextBeats，享受一个功能完整、界面精美的 Lofi 音乐播放器。无论是编程、学习还是放松，NextBeats 都能为你营造完美的音乐氛围。

本篇教程到此结束，祝你使用愉快！🎧

---

原文：<https://www.passnat.com/posts/nextbeats-retro-lofi-music-player-docker-deployment-guide-youtube-integration-sound-mixing-nas-remote-access>


## 相关文章

- [可视化Docker Compose管理工具，NAS极简部署Compose Craft，无公网内网穿透远程访问](compose-craft-docker-visualization-nas-deployment-internal-network-remote-access.md)
- [AI盘网盘搜索聚合平台，Next.js打造智能资源管理，NAS一键部署无需复杂配置，内网穿透远程访问。](ai-pan-wangpan-search-aggregation-platform-nextjs-intelligent-resource-management-nas-one-click-deployment-no-complex-configuration-intranet-penetration-remote-access.md)
- [本地密码生成器PSWD，隐私优先TypeScript构建，支持Docker和NAS快速部署，内网穿透远程访问](local-password-generator-pswd-privacy-first-typescript-docker-nas-deployment-intranet-penetration-remote-access.md)
- [MusicCard音乐海报生成器Docker部署指南，Next.js在线音乐海报转换工具内网穿透访问](music-card-docker-deployment-nextjs-music-poster-generator-intranet-penetration.md)
- [Hexon Hexo博客在线管理系统，图形化界面轻松管理文章，Docker极简部署内网穿透远程访问](hexon-hexo-blog-management-system-graphic-interface-docker-deployment.md)
