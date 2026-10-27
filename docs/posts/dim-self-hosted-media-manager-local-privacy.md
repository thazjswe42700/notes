---
title: "自托管媒体管理器Dim：极简配置打造私人媒体库，本地部署隐私安全有保障"
date: 2025-10-16
description: "Dim是自托管媒体管理器，自动整理本地媒体库，提供优雅网页界面。支持电影、电视剧等视频文件管理，完全掌控个人数据隐私。通过Docker快速部署，配置简单，支持硬件加速转码。打造属于自己的私人媒体中心，体验流畅媒体播放和管理。"
canonical: https://www.passnat.com/posts/dim-self-hosted-media-manager-local-privacy
---

## 项目介绍

Dim 是一款自托管的媒体管理器，专为本地媒体库的组织和管理而生。通过最小化的配置，Dim 能够自动整理和美化你的媒体收藏，并提供优雅的网页界面，让你随时随地访问和播放你的影视内容。无论是电影、电视剧还是其他视频文件，Dim 都能帮你打造一个属于自己的私人媒体中心。相比云端服务，Dim 让你完全掌控自己的媒体数据，隐私性更强，使用体验也更加流畅。

项目地址： [点我跳转](https://github.com/Dusk-Labs/dim)

## 部署安装

如果需要在外网也能访问你的媒体库，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 官方的全平台快速上手指南。

### 使用 Docker 快速部署

打开你的 NAS 设备或 Docker 管理界面，点击本地镜像-添加镜像-从 URL 添加。

在镜像 URL 输入框中填入：ghcr.io/dusk-labs/dim:dev，等待镜像下载完成后点击启动按钮。

**端口映射设置**

将容器内的 8000 端口映射到主机端口（可根据需要修改主机端口，但容器内端口必须保持为 8000）。如果 8000 端口已被占用，可以修改主机端口为其他未占用的端口号。

**存储位置配置**

需要创建以下目录映射：

配置目录：将主机上的 '$HOME/.config/dim' 映射到容器内的 '/opt/dim/config'，用于存储 Dim 的配置文件和数据库

媒体目录：将你存放媒体文件的目录映射到容器内（例如将 '/media' 映射为 '/media'），支持映射多个媒体目录

如果需要硬件加速功能（推荐在 NAS 上使用以提升转码性能），需要额外挂载设备：'/dev/dri/renderD128'

**环境变量设置**

Dim 无需特殊的环境变量配置，保持默认即可。

**启动容器**

勾选开机自动启动选项后，点击下一步启动容器。

创建成功后，打开浏览器访问 'http://你的设备IP:8000'（如果修改了端口则使用对应的端口号），即可看到 Dim 的媒体管理界面。

添加你的媒体目录后，Dim 会自动扫描和整理你的影视文件，为你呈现一个美观的媒体库界面。

## 总结

通过简单的 Docker 一键部署，你就能拥有一个功能完整的私人媒体中心。Dim 的自动整理功能让繁琐的媒体管理变得轻而易举，而本地部署也确保了你的媒体数据安全。现在就开始体验 Dim 带来的便利吧！

---

原文：<https://www.passnat.com/posts/dim-self-hosted-media-manager-local-privacy>


## 相关文章

- [自托管书签管理工具Servas无公网部署指南 NAS极简配置内网穿透远程访问](servas-self-hosted-bookmark-manager-nas-deployment-guide-intranet-penetration-remote-access.md)
- [自建私人网盘PicHome Docker快速部署，NAS一键启动智能媒体管理系统](pichome-docker-nas-private-cloud-storage-deployment.md)
- [开源Rust音乐服务器Diosic极简部署，NAS轻量级流媒体解决方案](diosic-rust-music-server-nas-deployment.md)
- [自托管任务管理系统tududi Docker极简部署 本地数据完全私密可控](self-hosted-task-management-tududi-docker-deployment.md)
- [自动化文件处理系统FileFlows，NAS部署媒体转码工具，内网穿透远程访问管理](fileflows-nas-media-transcoding-intranet-penetration.md)
