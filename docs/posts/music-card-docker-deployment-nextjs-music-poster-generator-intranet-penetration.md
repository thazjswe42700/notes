---
title: "MusicCard音乐海报生成器Docker部署指南，Next.js在线音乐海报转换工具内网穿透访问"
date: 2025-10-16
description: "MusicCard是一款基于Next.js14的在线音乐海报生成工具，支持将网易云音乐转换成精美可视化海报。提供多种主题布局、响应式设计、歌词展示和高清导出功能。本指南详细介绍Docker快速部署步骤、端口配置方法及访问使用说明，助您轻松搭建个人音乐海报生成器。"
canonical: https://www.passnat.com/posts/music-card-docker-deployment-nextjs-music-poster-generator-intranet-penetration
---

## MusicCard - 音乐海报生成器部署指南

### 项目介绍

MusicCard 是一款基于 Next.js 14 开发的在线音乐海报生成工具，专为音乐爱好者打造。它能够将你喜爱的网易云音乐转换成精美的可视化海报，支持多种主题和布局选择。

这个项目使用 TypeScript 和 Tailwind CSS 构建，提供了响应式设计，无论是在桌面还是移动设备上都能完美展现。用户可以自定义海报的样式和颜色，支持歌词展示，最后还能导出高清图片保存。整个工具开箱即用，无需复杂配置，轻松几步就能生成专属于你的音乐海报。

项目地址：[点我跳转](https://github.com/aidaox/MusicCard)

### 部署安装

如果你想在外网也能访问到自己部署的 MusicCard，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，具体操作方法可参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，这样就能随时随地访问你的音乐海报生成器了。

#### Docker 快速部署

打开你的 NAS 设备（飞牛 NAS 或其他支持 Docker 的设备）

点击 **本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：node:18-alpine（用于构建基础镜像）

等待镜像下载完成后，我们需要通过命令行方式部署。在你的 NAS 或服务器上执行以下命令：

```bash
docker run -d \
  --name musiccard \
  -p 3000:3000 \
  --restart unless-stopped \
  node:18-alpine
```

如果端口 3000 已被占用，可以将前面的 3000 修改为其他端口（如 3001、3002），但容器内的端口 3000 需要保持不变。

#### 配置说明

**端口映射设置：**

容器外部端口：3000（如有冲突可修改，例如改为 3001）

容器内部端口：3000（保持不变）

**存储配置：**

由于 MusicCard 主要用于生成海报，不需要持久化存储，所以无需额外配置存储目录。

**环境变量设置：**

该项目无需配置环境变量，使用默认设置即可正常运行。

#### 访问应用

部署完成后，在浏览器中访问：

```
http://你的NAS地址:3000
```

即可看到 MusicCard 的界面，开始生成你的专属音乐海报了！

### 使用建议

建议使用网易云音乐的分享链接，工具会自动解析歌曲信息

生成的海报支持多种主题选择，可根据个人喜好调整

导出的图片为高清格式，适合分享到社交媒体

本篇教程结束，祝你使用愉快！🎵

---

原文：<https://www.passnat.com/posts/music-card-docker-deployment-nextjs-music-poster-generator-intranet-penetration>


## 相关文章

- [AI盘网盘搜索聚合平台，Next.js打造智能资源管理，NAS一键部署无需复杂配置，内网穿透远程访问。](ai-pan-wangpan-search-aggregation-platform-nextjs-intelligent-resource-management-nas-one-click-deployment-no-complex-configuration-intranet-penetration-remote-access.md)
- [NotNav导航网站Notion数据库驱动，Next.js打造响应式设计支持内外网切换](notnav-notion-database-nextjs-responsive-design.md)
- [NextBeats复古Lofi音乐播放器部署指南，Docker一键启动，YouTube集成播放，音效混合定制，NAS内网穿透远程访问](nextbeats-retro-lofi-music-player-docker-deployment-guide-youtube-integration-sound-mixing-nas-remote-access.md)
- [自建私有图片视频分享平台Share Me,Next.js开源替代Imgur,NAS一键部署](self-hosted-private-image-video-sharing-platform-share-me-nextjs-open-source-imgur-alternative-nas-one-click-deploy.md)
- [开源日程管理系统Cal.com自建部署，完全控制数据NAS本地托管，支持内网穿透远程访问](open-source-calendar-management-cal-com-self-hosted-deployment-local-nas-storage-intranet-penetration-remote-access.md)
