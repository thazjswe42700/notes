---
title: "私有ntfy推送服务NAS极简部署，内网穿透外网实时通知接收方案"
date: 2025-10-16
description: "在NAS上快速部署ntfy推送通知服务，支持Docker一键启动。ntfy是免费开源的HTTP推送通知系统，采用发布-订阅模式，无需注册即可从任何设备向手机或桌面发送实时通知。本教程详细介绍了Docker容器配置、端口映射、存储卷设置等完整部署步骤，让你轻松在家中NAS或服务器上运行私有通知实例。"
canonical: https://www.passnat.com/posts/private-ntfy-push-service-nas-minimal-deployment-intranet-penetration-external-network-real-time-notification-solution
---

## 项目介绍

ntfy（发音为'notify'）是一个简洁而强大的 HTTP 推送通知服务。它采用发布-订阅模式设计，让你可以通过简单的 PUT/POST 请求从任何计算机向手机或桌面发送通知，完全免费且无需注册。无论是监控脚本的执行状态、接收服务器告警，还是获取重要提醒，ntfy 都能轻松胜任。

项目完全开源，支持自建部署。官方提供了 Android 和 iOS 移动应用，同时也提供了 Web 界面供浏览器使用。无论你是想快速体验，还是想在自己的 NAS 或服务器上部署私有实例，ntfy 都能满足你的需求。

项目地址：[点我跳转](https://github.com/binwiederhier/ntfy)

## 部署安装

如果你需要在外网也能访问自建的 ntfy 服务，建议配置内网穿透功能。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地推送通知了。具体操作步骤可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略。

### 最快上手方案（Docker 单命令）

打开你的 NAS 设备或任何支持 Docker 的系统，进入 Docker 管理界面。

**第一步：添加镜像**

点击'本地镜像'→'添加镜像'→'从 URL 添加'，在镜像地址栏填入：

```
binwiederhier/ntfy:latest
```

等待镜像下载完成。

**第二步：启动容器**

下载完成后，点击启动按钮。在容器配置中进行以下设置：

**基础配置**

勾选'开机自动启动'，确保 ntfy 服务随系统启动

**端口映射**

在端口配置中设置如下映射：

容器端口：`80`

主机端口：`80`（如果 80 端口被占用，可改为其他端口如 `8080`，但容器内端口保持 `80` 不变）

**存储卷配置**

ntfy 需要持久化存储来保存缓存数据和配置文件，请创建以下两个映射：

缓存数据目录

容器路径：`/var/cache/ntfy`

主机路径：`/mnt/data/ntfy/cache`（请提前在 NAS 上创建此文件夹）

配置文件目录

容器路径：`/etc/ntfy`

主机路径：`/mnt/data/ntfy/config`（请提前在 NAS 上创建此文件夹）

**环境变量设置**

可选设置，不填也可以正常运行：

`TZ`：时区设置，默认 `UTC`。如需使用本地时区，可设置为 `Asia/Shanghai`

其他配置保持默认即可，点击启动。

**第三步：访问服务**

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:80
```

或者如果你修改了端口为 8080，则访问：

```
http://你的NAS_IP:8080
```

此时你应该能看到 ntfy 的 Web 界面。你可以在这里订阅通知主题，或者通过 API 发送通知。

**快速测试**

在服务器或电脑上运行以下命令来测试 ntfy 是否正常工作：

```bash
curl -d 'Hello from ntfy!' http://你的NAS_IP:80/test_topic
```

然后在 Web 界面订阅 `test\_topic` 主题，就能收到通知了。

## 总结

通过以上步骤，你已经成功在 NAS 上部署了 ntfy 通知服务。现在你可以利用它来接收各种系统告警、脚本执行状态或自定义通知。结合内网穿透，即使在外也能实时接收来自家中服务器的重要消息。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/private-ntfy-push-service-nas-minimal-deployment-intranet-penetration-external-network-real-time-notification-solution>


## 相关文章

- [MicroBin 临时分享：NAS Docker 部署 + 内网穿透](microbin-lightweight-self-hosted-clipboard-docker-deployment-intranet-penetration.md)
- [PairDrop 自建隔空投送：飞牛 NAS Docker 部署 + 内网穿透跨设备传文件](pairdrop-cross-device-file-transfer-nas-docker-deployment-intranet-penetration.md)
- [ExpenseOwl 轻量记账：NAS Docker 部署 + 内网穿透](expenseowl-lightweight-expense-tracker-nas-docker-intranet-penetration.md)
- [HivisionIDPhotos 手机拍一张就出证件照：飞牛 NAS Docker 部署 + 内网穿透](hivision-id-photos-professional-certificate-photo-generator.md)
- [DopplerTask开源自动化平台Docker快速部署指南，NAS轻松搭建工作流自动化系统](doppler-task-docker-nas-workflow-automation-guide.md)
