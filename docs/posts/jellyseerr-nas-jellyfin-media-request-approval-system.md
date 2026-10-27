---
title: "Jellyseerr开源影视请求管理系统，NAS一键部署Jellyfin媒体库，内网穿透远程访问审批影视资源"
date: 2025-10-16
description: "Jellyseerr开源媒体库请求管理系统，支持Jellyfin、Plex、Emby等服务器，与Sonarr、Radarr无缝集成。提供友好的影视请求界面，支持权限管理、通知提醒、按季请求等功能。本文详细介绍Docker部署安装步骤、端口映射配置、环保变量设置方法及功能特性，助你快速搭建家庭影音请求平台。"
canonical: https://www.passnat.com/posts/jellyseerr-nas-jellyfin-media-request-approval-system
---

## 项目介绍

Jellyseerr 是一款开源免费的媒体库请求管理系统，专为家庭影音爱好者设计。它完美支持 Jellyfin、Plex 和 Emby 等主流媒体服务器，并能与 Sonarr 和 Radarr 等下载工具无缝集成。

通过 Jellyseerr，你可以搭建一个属于自己的影视请求平台。家人和朋友可以通过友好的界面申请他们想看的电影和电视剧，而你则可以轻松管理这些请求。系统支持按季度请求、权限管理、通知提醒等功能，让整个流程变得简洁高效。无论是在家还是外出，都能通过手机随时审批请求。

项目地址：[点我跳转](https://github.com/Fallenbagel/jellyseerr)

## 部署安装

如果你想在外网也能访问 Jellyseerr，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的配置步骤可以参考 [这份完整指南](https://www.passnat.com/posts/p)，涵盖全平台快速上手的方法。

### Docker 单命令部署

最简单的部署方式是使用 Docker 单命令启动。打开你的飞牛 NAS 或其他 NAS 设备，按照以下步骤操作：

添加 Docker 镜像

点击'本地镜像' → '添加镜像' → '从 URL 添加'

镜像地址填写：fallenbagel/jellyseerr:latest

等待镜像下载完成后点击启动

容器配置

勾选'开机自动启动'以便系统重启后自动运行

点击下一步进入端口设置

端口映射设置

容器内部端口：5055

宿主机映射端口：5055（如果该端口已被占用，可修改宿主机端口号，比如改为 5056，但容器内端口保持 5055 不变）

存储配置

创建一个本地文件夹用于存储 Jellyseerr 的配置文件和数据库

将容器内的 /app/config 目录映射到这个本地文件夹

这样即使容器重启，你的所有配置和历史记录都会保留

环境变量设置

必须设置：

TZ：时区设置，建议填写 Asia/Shanghai（根据你的地区调整）

可选设置：

LOG\_LEVEL：日志级别，默认为 info

PORT：应用监听端口，默认为 5055

其他环境变量保持默认即可，直接下一步启动容器。

访问应用

容器启动成功后，在浏览器中访问 http://你的NAS地址:5055

首次访问会进入初始化设置页面，按照提示连接你的媒体服务器（Jellyfin/Plex/Emby）

配置完成后即可开始使用

## 功能特性一览

✅ 完整的媒体服务器集成（Jellyfin/Emby/Plex）

✅ 用户认证与权限管理

✅ 灵活的请求系统（支持按季请求）

✅ 简洁的请求审批界面

✅ 多种通知方式支持

✅ 移动设备友好的响应式设计

✅ 媒体库自动扫描

✅ 支持 PostgreSQL 和 SQLite 数据库

✅ 观看列表与黑名单功能

本篇教程结束

---

原文：<https://www.passnat.com/posts/jellyseerr-nas-jellyfin-media-request-approval-system>
