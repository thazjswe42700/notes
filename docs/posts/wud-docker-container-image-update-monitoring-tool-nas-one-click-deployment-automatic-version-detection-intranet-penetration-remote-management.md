---
title: "WUD Docker容器镜像更新监控工具，NAS一键部署，自动检测镜像版本，内网穿透远程管理"
date: 2025-10-16
description: "WUD（What's up Docker）是强大的Docker容器镜像更新监控工具，自动检测镜像新版本并提示更新。支持Web界面查看所有容器镜像版本信息，适合NAS用户和Docker爱好者自动化管理容器更新，确保容器镜像始终保持最新状态，及时获取安全更新。"
canonical: https://www.passnat.com/posts/wud-docker-container-image-update-monitoring-tool-nas-one-click-deployment-automatic-version-detection-intranet-penetration-remote-management
---

## 项目介绍

WUD（What's up Docker）是一款强大的 Docker 容器镜像更新监控工具。它能够自动检测你的 Docker 容器中使用的镜像是否有新版本可用，帮助你及时了解容器镜像的更新状态。无论你是运行单个容器还是管理多个服务，WUD 都能让你轻松掌握每个镜像的最新动态，确保你的容器始终保持最新状态。

这个项目非常适合 NAS 用户和 Docker 爱好者，特别是那些希望自动化管理容器镜像更新的用户。通过 WUD，你可以通过友好的 Web 界面查看所有容器的镜像版本信息，再也不用担心错过重要的安全更新。

项目地址：[点我跳转](https://github.com/fmartinou/whats-up-docker)

## 部署安装

如果你需要在家庭网络外部也能访问 WUD，建议配置内网穿透服务。可以使用[帕斯内网穿透](https://passnat.com)来实现，先注册好账号备用。具体操作步骤可以参考[这份全平台上手攻略](https://www.passnat.com/posts/p)，轻松实现远程访问。

### 使用 Docker 单命令快速部署

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理界面。

点击**本地镜像**- **添加镜像**- **从 URL 添加**。

在镜像地址栏填入以下镜像地址：

```
getwud/wud:latest
```

等待镜像下载完成后，点击**启动**按钮启动容器。

**端口配置**

在容器启动配置中，需要进行端口映射设置。WUD 默认运行在容器内的**3000 端口**。如果你的 NAS 上 3000 端口已被占用，可以修改宿主机端口（例如改为 3001、3002 等），但容器内部端口应保持为 3000。

**存储配置**

WUD 需要持久化存储其配置和数据。建议在你的 NAS 上创建一个文件夹（例如 `/data/wud`），然后将其映射到容器内的 `/data` 目录，确保容器重启后数据不会丢失。

**环境变量设置**

WUD 支持多个环境变量配置，以下是常用的可选设置：

环境变量 | 说明 | 默认值
`LOG\_LEVEL` | 日志级别 | `info`
`REGISTRY\_URL` | Docker 镜像仓库地址 | `https://registry-1.docker.io`
`WATCH\_INTERVAL` | 检查更新的间隔时间（秒） | `3600`

这些环境变量都是可选的，如果你不需要特殊配置，可以直接使用默认值。对于大多数用户来说，无需设置任何环境变量，直接启动容器即可正常使用。

配置完成后点击**启动**按钮。

**访问应用**

创建成功后，打开浏览器访问 `http://你的NAS地址:3000`（如果修改了端口号，请使用对应的端口），就能看到 WUD 的 Web 界面了。此时你可以开始配置要监控的 Docker 容器，WUD 会自动检测这些容器中镜像的更新状态。

## 总结

通过以上步骤，你已经成功部署了 WUD 容器镜像监控工具。现在你可以在一个统一的界面中查看所有 Docker 容器的镜像更新情况，再也不用手动逐个检查。定期检查 WUD 的提示，及时更新过期的镜像，让你的容器环境始终保持最佳状态。

---

原文：<https://www.passnat.com/posts/wud-docker-container-image-update-monitoring-tool-nas-one-click-deployment-automatic-version-detection-intranet-penetration-remote-management>


## 相关文章

- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [自托管即时通讯平台Stoat完整部署指南，内网穿透远程访问无公网限制](self-hosted-stoat-instant-messaging-deployment-guide-intranet-penetration-remote-access-no-public-ip.md)
- [免费在线虚拟钢琴演奏平台 AutoPiano NAS 极简部署 内网穿透远程弹奏](free-online-virtual-piano-autopiano-nas-simple-deployment-intranet-penetration-remote-playing.md)
- [Yacht容器管理系统一键部署指南，Docker应用模板快速启动，NAS自建服务完整教程](yacht-container-management-one-click-deployment-guide-docker-application-templates-quick-start-nas-self-built-services-complete-tutorial.md)
