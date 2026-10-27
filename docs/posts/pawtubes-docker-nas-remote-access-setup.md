---
title: "网页广播播放器PawTunes部署教程 Docker一键启动NAS内网穿透远程访问"
date: 2025-10-16
description: "PawTunes是功能强大的网页版互联网广播播放器，汇集15年开发经验精心打造。支持无限频道配置、多码率流媒体、多语言支持、动态播放列表生成，集成Spotify、iTunes、FanArt TV等API获取专辑封面。界面精美响应式，完美适配桌面、平板和手机。所有音乐资源由电台直播流提供，支持Docker一键部署，随时随地畅享高质量广播内容。"
canonical: https://www.passnat.com/posts/pawtubes-docker-nas-remote-access-setup
---

## 项目介绍

PawTunes 是一款功能强大的网页版互联网广播播放器，集合了15年以上的开发经验精心打造而成。它不仅仅是一个简单的音乐播放工具，而是一个完整的广播电台平台应用，能够自动获取直播电台的曲目信息和专辑封面。

这个项目支持无限数量的频道配置、多码率流媒体选择、多语言支持、动态播放列表生成，以及来自Spotify、iTunes、FanArt TV等多个API的专辑封面管理。无论你是广播电台运营者还是音乐爱好者，PawTunes都能为你提供专业级的播放体验。

界面设计精美，支持响应式布局，在桌面、平板和手机上都能完美显示。更重要的是，所有的音乐资源都由电台直播流提供，你无需自备任何资源，随时随地都能畅享高质量的广播内容。

项目地址：[点我跳转](https://github.com/Jackysi/PawTunes)

## 部署安装

若想在外网也能访问你的PawTunes播放器，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先行注册账号以备后用。具体的穿透配置步骤可参考 [全平台快速上手指南](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### Docker 一键部署（推荐）

PawTunes 最简便的部署方式就是使用 Docker，只需一条命令即可快速启动：

打开你的 NAS 设备（飞牛NAS、群晖等均可）或任何支持Docker的系统，点击「本地镜像」-「添加镜像」-「从URL添加」。

在镜像地址栏填入：

```
jackyprahec/pawtunes:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

### 容器配置步骤

**开机自动启动**

勾选「开机自动启动」选项，确保容器随系统启动自动运行。

**端口映射设置**

在端口配置处，设置以下映射关系：
容器端口：80
主机端口：80（如果80端口被占用，可修改为其他端口，如8080、8888等，但容器内端口保持80不变）

**存储位置配置**

为了保证配置和数据在容器更新后不丢失，需要挂载以下三个目录：

容器内路径：/var/www/html/inc/config，用途说明：存储general.php等配置文件，本地挂载建议：/local/pawtunes/config
容器内路径：/var/www/html/inc/locale，用途说明：存储多语言翻译文件，本地挂载建议：/local/pawtunes/locale
容器内路径：/var/www/html/data，用途说明：存储专辑封面、缓存和图片，本地挂载建议：/local/pawtunes/data

在NAS中创建这些文件夹，然后在容器配置中将其映射到对应的容器内路径。

**环境变量设置**

PawTunes 的环境变量配置相对简洁，通常无需特殊设置即可正常运行。如无特殊需求，可直接跳过此步骤，点击「下一步」启动容器。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS地址:80
```

或如果修改了端口（比如改为8080），则访问：

```
http://你的NAS地址:8080
```

进入PawTunes的控制面板，使用默认登录凭证：
用户名：admin
密码：password

登录后即可开始配置你的广播频道、设置API密钥（Spotify、LastFM等）、自定义主题样式等功能。

重要提示：首次运行时，系统会自动将默认配置文件复制到挂载的卷中，无需手动操作。

到此，PawTunes 的部署就已完成！你现在拥有了一个功能完整、专业级的互联网广播播放平台。无论是个人使用还是电台运营，都能为你提供出色的播放体验。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/pawtubes-docker-nas-remote-access-setup>


## 相关文章

- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问](appsmith-nas-docker-compose-local-deployment-guide.md)
- [无代码网页数据提取Maxun自建指南，2分钟训练机器人自动爬取，内网穿透远程访问](no-code-web-scraping-maxun-guide-2-minute-robot-training-intranet-penetration-remote-access.md)
- [开源问卷调查系统LimeSurvey，NAS一键部署，内网穿透远程访问，无限问卷模板](open-source-survey-system-limesurvey-nas-one-click-deployment-intranet-penetration-remote-access-unlimited-questionnaire-templates.md)
- [自建私人音乐统计库 Maloja Docker 一键部署 NAS 内网穿透远程访问](self-hosted-music-stats-maloja-docker-nas-remote-access.md)
