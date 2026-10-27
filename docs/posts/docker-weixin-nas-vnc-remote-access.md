---
title: "Docker容器运行微信应用，Web远程访问与VNC连接，NAS一键部署搭建家庭微信服务器"
date: 2025-10-16
description: "Docker-WeChat是创新的容器化微信解决方案，支持在Docker环境中运行微信应用。通过Web浏览器或VNC客户端远程访问，让你在任何设备上随时使用微信。本文详细介绍快速部署步骤、容器配置、端口映射、环境变量设置及访问方式，帮助用户在NAS设备上轻松搭建跨平台微信服务。"
canonical: https://www.passnat.com/posts/docker-weixin-nas-vnc-remote-access
---

## 项目介绍

Docker-WeChat 是一个创新的容器化解决方案，让你可以在 Docker 环境中运行微信应用。这个项目将微信完整地封装在容器内，支持通过 Web 浏览器或 VNC 客户端远程访问，让你可以在任何设备上随时随地使用微信。无论你是在办公室、家里还是外出，都能通过网络连接到你的微信应用，实现真正的跨平台、跨地域的微信使用体验。

项目地址：[点我跳转](https://github.com/RICwang/docker-wechat)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透服务，推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的操作方法可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略，让你随处都能连接到家中的微信。

### 快速部署

打开飞牛 NAS 或其他 NAS 设备的 Docker 管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**。

镜像地址填入：ricwang/docker-wechat:latest

等待镜像下载完成后，点击启动按钮启动容器。

### 容器配置

**勾选开机自动启动**，然后进入下一步。

**端口设置**：该项目需要映射两个端口

容器内端口 5800 → 宿主机端口 5800（Web 访问端口）

容器内端口 5900 → 宿主机端口 5900（VNC 访问端口）

如果提示端口被占用，可以修改宿主机端口号（冒号前面的数字），例如改为 5801:5800 或 5901:5900，但容器内的端口保持不变。

**存储位置**：该项目需要持久化以下目录来保存微信数据和文件

创建文件夹 .xwechat 用于存储微信配置文件，映射到容器内的 /root/.xwechat

创建文件夹 xwechat\_files 用于存储微信文件，映射到容器内的 /root/xwechat\_files

创建文件夹 downloads 用于存储下载的文件，映射到容器内的 /root/downloads

映射 /dev/snd 用于音频支持

### 环境变量设置

**必须设置**：

LANG: zh\_CN.UTF-8（设置中文语言环境）

TZ: Asia/Shanghai（设置时区为上海）

USER\_ID: 0（用户 ID）

GROUP\_ID: 0（用户组 ID）

WEB\_AUDIO: 1（启用 Web 音频支持）

privileged: true（以特权模式运行）

**可选设置**：

KEEP\_APP\_RUNNING: 0（默认）- 设置为 1 可在微信崩溃时自动重启

DISPLAY\_WIDTH: 1920（默认）- 调整显示窗口宽度

DISPLAY\_HEIGHT: 1080（默认）- 调整显示窗口高度

DARK\_MODE: 0（默认）- 设置为 1 启用深色模式

WEB\_AUTHENTICATION: 0（默认）- 设置为 1 启用 Web 访问密码保护

WEB\_AUTHENTICATION\_USERNAME: 配置 Web 访问用户名

WEB\_AUTHENTICATION\_PASSWORD: 配置 Web 访问密码

VNC\_PASSWORD: 配置 VNC 连接密码

其他变量保持默认即可，直接下一步启动容器。

### 访问应用

创建成功后，可以通过以下方式访问微信：

**Web 访问**：在浏览器中输入 http://你的NAS地址:5800 即可打开微信

**VNC 访问**：使用 VNC 客户端连接到 你的NAS地址:5900

到这里就可以正常使用微信了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/docker-weixin-nas-vnc-remote-access>
