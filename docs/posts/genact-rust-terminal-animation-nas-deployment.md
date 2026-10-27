---
title: "装忙神器Genact：Rust编写跨平台终端动画，NAS一键部署模拟工作场景"
date: 2025-10-16
description: "Genact是一款Rust编写的跨平台\"装忙\"工具，能模拟编译、下载、挖矿等真实计算机工作场景。支持Linux、macOS、Windows等系统，提供16种模块和网页版本。本文详细介绍Docker部署、参数配置、NAS安装及网页版使用方法，帮助用户快速体验逼真的终端动画效果。"
canonical: https://www.passnat.com/posts/genact-rust-terminal-animation-nas-deployment
---

## 项目介绍

Genact 是一款创意十足的'装忙'工具，它能模拟各种真实的计算机工作场景，让你在需要时可以逼真地展示自己在忙碌工作。无论是编译代码、下载文件、挖矿还是系统日志，Genact 都能完美呈现各种酷炫的终端动画效果。

这个项目用 Rust 编写，具有跨平台特性，支持 Linux、macOS、Windows、FreeBSD 等多种操作系统，甚至还提供了在线网页版本，无需安装任何依赖即可直接使用。项目内置了 16 种不同的模块场景，每一种都能生成逼真的伪工作动画，让你的屏幕看起来充满了专业的技术范儿。

项目地址： [点我跳转](https://github.com/svenstaro/genact)

## 部署安装

如果你想在远程设备上运行此项目，建议配置内网穿透工具以便随时随地访问。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即使在公司或外出时也能轻松连接到家里的设备。详细的使用指南请查看 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p)。

### 最快速上手方案 - Docker 单命令启动

Genact 提供了官方 Docker 镜像，这是最便捷的部署方式。只需一条命令即可立即体验：

```bash
docker run -it --rm svenstaro/genact
```

这条命令会直接启动容器并在终端中运行 Genact，展示随机的活动模拟场景。

### 自定义运行选项

如果你想运行特定的模块或调整速度，可以添加相应的参数：

```bash
docker run -it --rm svenstaro/genact -m cc,cargo,memdump -s 2
```

参数说明：

-m 或 --modules：指定要运行的模块（可选值：ansible、bootlog、botnet、bruteforce、cargo、cc、composer、cryptomining、docker\_build、docker\_image\_rm、download、julia、kernel\_compile、memdump、mkinitcpio、rkhunter、simcity、terraform、weblog）

-s 或 --speed-factor：调整动画播放速度（默认值为 1，数值越大播放越快）

-l 或 --list-modules：列出所有可用的模块

### 在 NAS 设备上部署

如果你使用飞牛 NAS 或其他 NAS 设备，可以按以下步骤操作：

打开 NAS 管理界面，进入 Docker 应用管理区域

选择'本地镜像' → '添加镜像' → '从 URL 添加'

在镜像地址栏填入：svenstaro/genact:latest

等待镜像下载完成后，点击'启动'按钮

### 启动容器配置

在启动容器前，你可以进行以下配置：

**勾选开机自启**：建议勾选'开机自动启动'选项，这样 NAS 重启后容器会自动运行。

**端口映射**（可选）：

容器内部使用标准输出，通常不需要映射端口

如果需要通过网络访问，可以将容器的标准输入/输出映射到宿主机

**存储卷配置**（可选）：

此项目无需持久化存储，可以不配置存储卷

如果想保存运行日志，可创建一个文件夹并将其映射到容器内

**环境变量设置**：

此项目无需设置特殊的环境变量，保持默认配置即可直接使用。

### 查看帮助信息

如果需要了解所有可用的命令选项，可以运行：

```bash
docker run -it --rm svenstaro/genact -h
```

或者在线访问网页版本体验：https://svenstaro.github.io/genact/

### 网页版本使用

项目还提供了便捷的在线网页版本，无需任何安装步骤，直接在浏览器中打开即可使用。你可以通过 URL 参数自定义运行效果：

```
https://svenstaro.github.io/genact?module=cc&module=memdump&speed-factor=2
```

通过以上方式，你可以快速部署并运行 Genact，随时随地展现你的'忙碌'姿态。无论是在工作中需要一点小幽默，还是想要体验炫酷的终端动画效果，Genact 都能完美满足你的需求。

本篇教程结束

---

原文：<https://www.passnat.com/posts/genact-rust-terminal-animation-nas-deployment>


## 相关文章

- [自建RustDesk远程桌面服务器,NAS极简部署无公网穿透远程控制,开源跨平台屏幕共享工具](rustdesk-nas-remote-desktop-server-self-hosted.md)
- [阿里云盘CLI命令行工具NAS部署指南，本地文件云端备份同步方案详解](aliyun-pan-cli-nas-deployment-guide-local-cloud-backup-sync-solution.md)
- [无状态密码管理器Heimdallr，一密掌控所有账户，NAS本地部署零风险，内网穿透远程访问无忧](heimdallr-stateless-password-manager-nas-local-deployment.md)
- [自建私人漫画库 Suwayomi 服务器，无限漫画源在线阅读，NAS 一键部署内网穿透远程访问](suwayomi-private-manga-server-nas-deployment.md)
- [DweebUI容器管理面板轻松上手，Docker Podman全能卫士一键部署运维](dweebui-container-management-panel-docker-podman-deployment.md)
