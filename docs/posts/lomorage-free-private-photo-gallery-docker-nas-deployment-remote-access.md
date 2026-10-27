---
title: "免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库"
date: 2025-10-16
description: "Lomorage是免费私有云相册系统，支持Mac/Linux/Windows跨平台部署。提供iOS、Android原生客户端和Web浏览器访问，完全免费无隐藏费用。支持16TB硬盘、多种文件系统，可在旧笔记本、服务器或树莓派上部署。通过Docker快速安装，配置内网穿透实现远程访问，是家庭和小型团队的安全照片存储解决方案。"
canonical: https://www.passnat.com/posts/lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access
---

## 项目介绍

Lomorage 是一个功能强大的私有云相册系统，专为家庭用户和小型团队打造。它支持跨平台部署（Mac/Linux/Windows），提供原生的 iOS、Android 客户端和 Web 浏览器访问。无论你是想搭建一个家庭相册库，还是需要一个安全的私有照片存储方案，Lomorage 都能满足你的需求。

最大的亮点是**完全免费**，没有任何隐藏费用。你可以在任何旧笔记本、服务器或树莓派等单板计算机上部署，支持 FAT32、NTFS、EXT 等主流文件系统，最大支持 16TB 硬盘。只需安装免费的 Lomorage 服务器软件和客户端应用，就能拥有一个属于自己的私有相册系统。

unknown node[点我跳转](https://github.com/lomorage/)

## 部署安装

unknown node[帕斯内网穿透](https://passnat.com)unknown node[内网穿透全平台快速上手指南](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### 使用 Docker 快速部署

打开你的 NAS 设备或 Docker 管理界面，进入本地镜像管理模块，选择'从 URL 添加镜像'的选项。

在镜像地址栏中填入：`lomorage/lomorage:latest`

等待镜像下载完成后，点击启动按钮启动容器。

**容器启动配置：**

勾选'开机自动启动'选项，确保设备重启后服务能自动恢复运行。

**端口映射设置：**

Lomorage 默认使用 **8000** 端口提供 Web 服务。在端口映射配置中，将容器内的 8000 端口映射到宿主机的 8000 端口（或其他未被占用的端口）。如果你的 8000 端口已被其他应用占用，可以修改为其他端口号，比如 8001、8002 等，但容器内部的端口保持 8000 不变。

**存储位置配置：**

Lomorage 需要持久化存储相册数据。建议在宿主机上创建一个专用文件夹（例如 `/data/lomorage`），然后将其映射到容器内的 `/opt/lomorage` 目录，确保你的相册数据不会因容器重启而丢失。

**环境变量设置：**

通常情况下，Lomorage 无需额外的环境变量配置，保持默认即可正常运行。如果你有特殊需求，可在启动前咨询官方文档。

配置完成后，直接点击启动即可。

**访问应用：**

启动成功后，打开浏览器访问 `http://你的设备IP:8000`，就能看到 Lomorage 的 Web 界面了。首次访问可能需要进行初始化设置，按照界面提示完成即可。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access>


## 相关文章

- [Standard Notes 私密笔记自建 - 端到端加密开源方案，NAS 极简部署指南](standard-notes-private-notes-self-hosted-end-to-end-encryption-open-source-nas-simple-deployment-guide.md)
- [开源AI工作空间AIaW：跨平台客户端，集成多家大模型，NAS本地部署内网穿透远程访问](aiaw-open-source-ai-workspace-cross-platform-client-multi-model-integration-nas-local-deployment-intranet-penetration-remote-access.md)
- [私有部署开源聊天应用VoceChat，NAS轻松搭建安全私密聊天系统，内网穿透远程访问](vocechat-private-deployment-nas-internal-network-penetration.md)
- [Docker容器运行Windows系统 KVM加速极速部署 支持远程RDP连接访问](docker-windows-kvm-remote-rdp.md)
- [DweebUI容器管理面板轻松上手，Docker Podman全能卫士一键部署运维](dweebui-container-management-panel-docker-podman-deployment.md)
