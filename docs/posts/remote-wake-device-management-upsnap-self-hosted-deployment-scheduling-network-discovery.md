---
title: "远程唤醒设备管理工具 UpSnap 自建部署，支持定时任务与网络发现"
date: 2025-10-16
description: "UpSnap是简洁高效的远程唤醒管理工具，基于SvelteKit、Go和PocketBase构建。提供直观网页仪表板，支持WOL协议远程启动设备、定时任务自动化、端口监测、设备自动发现、用户权限管理、35+主题切换、多语言支持和Docker多平台部署。完全自建架构，数据安全可靠。"
canonical: https://www.passnat.com/posts/remote-wake-device-management-upsnap-self-hosted-deployment-scheduling-network-discovery
---

## 项目介绍

UpSnap 是一个简洁高效的远程唤醒管理工具，使用 SvelteKit、Go 和 PocketBase 构建而成。它提供了一个直观的网页仪表板，让你可以轻松地通过网络唤醒协议（WOL）远程启动本地网络中的设备。

无论你是想远程唤醒 NAS、服务器还是其他设备，UpSnap 都能满足你的需求。它支持设备自动发现、定时任务、端口监测等多项实用功能，配置简单，开箱即用。项目采用自建架构，数据完全掌握在自己手中，安全可靠。

[点我跳转](https://github.com/seriousm4x/UpSnap)

**核心特性：**

🚀 一键唤醒仪表板 - 快速启动网络中的任何设备

⏰ 定时任务自动化 - 支持 Cron 表达式设置计划任务

🔌 端口监测 - 实时检测设备状态

🔍 网络设备发现 - 自动扫描并识别本地网络设备

👤 用户权限管理 - 为不同用户分配差异化权限

🎨 35+ 主题切换 - 满足个性化审美需求

🌐 多语言支持 - 覆盖全球主流语言

🐳 多平台 Docker 镜像 - 支持 amd64、arm64、arm/v7、arm/v6 架构

🏠 完全自建 - 隐私安全有保障

## 部署安装

如果你需要在外网也能访问 UpSnap，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) 来实现远程访问，先注册账号备用。具体的配置步骤可以查看 [内网穿透全平台使用指南](https://www.passnat.com/posts/p)。

### 使用 Docker 快速部署

打开你的 NAS 设备或其他支持 Docker 的系统。

**第一步：添加镜像**

进入容器管理界面，点击本地镜像 - 添加镜像 - 从 URL 添加。

镜像地址填写：`ghcr.io/seriousm4x/upsnap:5`（也可使用 Docker Hub 的镜像：`seriousm4x/upsnap:5`）

等待镜像下载完成。

**第二步：启动容器**

点击启动按钮启动容器。

**第三步：配置容器参数**

勾选开机自动启动，然后进行以下配置：

**端口映射设置**

将容器内的 `8090` 端口映射到主机。如果你的主机上 8090 端口已被占用，可以修改映射的主机端口（例如改为 5000），但容器内的端口需要保持为 8090。

**存储配置**

创建一个本地文件夹用于存储应用数据（建议命名为 `data`），然后将其挂载到容器内的 `/app/pb\_data` 路径。这样可以确保你的设备配置和数据在容器重启后不会丢失。

**环境变量配置**

以下是 UpSnap 支持的环境变量设置：

**必须设置的环境变量：** 无

**可选的环境变量：**

TZ：设置容器时区（例如 `Europe/Berlin`），用于 Cron 任务的时间计算

UPSNAP\_INTERVAL：设备 Ping 检测的时间间隔，默认 `\*/10 \* \* \* \* \*`（每 10 秒检测一次）

UPSNAP\_SCAN\_RANGE：本地网络扫描范围（例如 `192.168.1.0/24`），用于自动发现设备

UPSNAP\_SCAN\_TIMEOUT：网络扫描超时时间，默认 `500ms`

UPSNAP\_PING\_PRIVILEGED：是否使用特权模式进行 Ping 操作，默认 `true`（如果容器非 root 运行则设为 `false`）

UPSNAP\_WEBSITE\_TITLE：自定义网站标题

如果你不需要特殊配置，可以直接跳过环境变量设置，使用默认配置启动。

**第四步：完成部署**

所有配置完成后，点击启动。容器启动成功后，在浏览器中访问 `http://你的NAS地址:8090`（如果修改了端口映射，请使用对应的端口）即可打开 UpSnap 管理界面。

首次访问时，系统会引导你创建管理员账户。创建完成后，你就可以开始添加要管理的设备了。

**部署完成！**

现在你已经成功部署了 UpSnap。你可以在仪表板上添加网络中的设备，设置定时唤醒任务，或者让系统自动发现并管理你的设备。享受远程设备管理的便利吧！

---

原文：<https://www.passnat.com/posts/remote-wake-device-management-upsnap-self-hosted-deployment-scheduling-network-discovery>


## 相关文章

- [自建私有图片视频分享平台Share Me,Next.js开源替代Imgur,NAS一键部署](self-hosted-private-image-video-sharing-platform-share-me-nextjs-open-source-imgur-alternative-nas-one-click-deploy.md)
- [本地私有AI聊天助手 Serge 离线运行LLM 无需API密钥 NAS极简部署内网穿透远程访问](serge-offline-llm-chat-assistant-nas-deployment.md)
- [开源自托管项目管理工具Focalboard部署指南，NAS内网穿透远程访问完整教程](focalboard-open-source-self-hosted-project-management-deployment-guide-nas-internal-penetration-remote-access-tutorial.md)
- [BGmi番剧自动追踪管理工具Docker部署指南，NAS内网穿透远程追番解决方案](bgmi-anime-tracker-docker-nas-remote-access.md)
- [无需命令行Docker可视化管理工具，DockerUI网页界面轻松管理容器与镜像，支持NAS内网穿透部署](docker-ui-web-management-container-image-nas-intranet-penetration.md)
