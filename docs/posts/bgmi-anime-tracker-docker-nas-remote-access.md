---
title: "BGmi番剧自动追踪管理工具Docker部署指南，NAS内网穿透远程追番解决方案"
date: 2025-10-16
description: "BGmi是Python开发的番剧自动追踪管理工具，支持命令行和网页操作。集成多个数据源和下载工具，提供网页管理界面、弹幕功能、RSS Feed和日历格式。Docker部署简便，配置8888端口、存储映射和环境变量即可快速启用。支持内网穿透外网访问，一站式解决番剧订阅下载管理需求。"
canonical: https://www.passnat.com/posts/bgmi-anime-tracker-docker-nas-remote-access
---

## 项目介绍

BGmi 是一个功能强大的番剧自动追踪与管理工具，采用 Python 开发，提供了命令行和网页两种使用方式。无论你是动漫爱好者还是追番达人，BGmi 都能帮助你轻松管理订阅的番剧，自动下载最新剧集，让追番变得更加便捷高效。

该项目支持多个数据源（bangumi.moe、蜜柑计划、DMHY 等），集成了多种下载工具（aria2、Transmission、qBittorrent、Deluge），提供了美观的网页管理界面，支持弹幕功能，还能生成 RSS Feed 和日历格式，真正做到了一站式番剧管理解决方案。

[点我跳转](https://github.com/BGmi/BGmi)

## 部署安装

如需在外网也能访问你的 BGmi 服务，建议配置内网穿透工具，推荐使用 [帕斯内网穿透](https://passnat.com)。先注册好账号，然后按照 [帕斯内网穿透全平台上手攻略](https://www.passnat.com/posts/p) 进行配置，这样就能在任何地方访问你的番剧库了。

### Docker 部署方式

BGmi 官方提供了完整的 Docker 解决方案。打开你的 NAS 设备或 Docker 管理面板，点击 **本地镜像 - 添加镜像 - 从 URL 添加**。

在镜像地址栏填入：bgmi/bgmi:latest，等待镜像下载完成后点击启动按钮。

#### 端口配置

BGmi 默认使用 **8888 端口**提供网页服务。在容器启动配置中，需要将容器内的 8888 端口映射到宿主机的某个端口（比如 8888）。如果你的宿主机 8888 端口已被占用，可以改为其他端口，比如 8889、9999 等，只需保证容器内部始终使用 8888 即可。

#### 存储配置

BGmi 需要持久化存储番剧数据和配置文件。建议在 NAS 上创建一个专用文件夹（如 /bgmi/data），然后将其映射到容器内的 /root/.bgmi 目录。同时创建另一个文件夹用于存放下载的番剧文件（如 /bgmi/bangumi），映射到容器内的 /root/.bgmi/bangumi。

#### 环境变量设置

以下为常用的环境变量配置：

必须设置：

BGMI\_HTTP\_ADMIN\_TOKEN：Web UI 的访问密码，建议设置一个复杂的密钥，如 'your-secret-token-here'

可选设置：

BGMI\_DATA\_SOURCE：番剧数据源，可选值为 'bangumi\_moe'（默认）、'mikan\_project' 或 'dmhy'

BGMI\_DOWNLOAD\_DELEGATE：下载工具，可选值为 'aria2-rpc'（默认）、'transmission-rpc'、'qbittorrent-webapi' 或 'deluge-rpc'

BGMI\_ARIA2\_RPC\_URL：如果使用 aria2，需配置其 RPC 地址，如 'http://localhost:6800/rpc'

BGMI\_PROXY：HTTP 代理地址（可选），如 'http://127.0.0.1:1080'

其他环境变量无需配置，保持默认即可。

#### 启动容器

完成上述配置后，点击下一步启动容器。勾选 **开机自动启动** 选项，这样 NAS 重启后 BGmi 会自动运行。

#### 访问服务

容器启动成功后，在浏览器中访问 'http://你的NAS地址:8888'（根据你实际映射的端口修改），输入之前设置的 'BGMI\_HTTP\_ADMIN\_TOKEN' 密码即可进入 BGmi 网页管理界面。

到这里，BGmi 的部署就全部完成了。你现在可以在网页上订阅喜欢的番剧，设置下载规则，BGmi 会自动为你追踪和下载最新的剧集。

本篇教程结束

---

原文：<https://www.passnat.com/posts/bgmi-anime-tracker-docker-nas-remote-access>
