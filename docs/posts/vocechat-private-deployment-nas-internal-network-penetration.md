---
title: "私有部署开源聊天应用VoceChat，NAS轻松搭建安全私密聊天系统，内网穿透远程访问"
date: 2025-10-16
description: "VoceChat是轻量级开源聊天应用，用Rust编写，仅需20MB即可部署。提供完整API和SDK，支持Android、iOS、macOS、Web、Windows多平台。40000+用户信赖，可在NAS和树莓派运行。所有数据自主存储，隐私安全有保障。支持Docker一键部署，无需复杂配置。"
canonical: https://www.passnat.com/posts/vocechat-private-deployment-nas-internal-network-penetration
---

## 项目介绍

**VoceChat** 是一款轻量级、私有部署的开源聊天应用，采用 Rust 编写，具有超强的性能和安全性。它提供完整的 API 和 SDK，让你可以轻松构建自己的应用内消息功能，无需依赖任何中央服务。

项目支持多平台部署，包括 Android、iOS、macOS、Web 和 Windows，已被来自全球 50+ 个国家的超过 40,000 名用户信赖。无论是个人使用还是企业级应用，VoceChat 都能提供安全、稳定、高效的私有聊天解决方案。

最大的亮点是服务器体积仅需 20MB，可以轻松运行在 NAS 和树莓派上，完全不占用系统资源。所有数据都存储在你自己的服务器上，隐私得到最大程度的保护。

项目地址：[点我跳转](https://voce.chat)

## 部署安装

如果你想在外网也能访问到部署的 VoceChat 服务，建议配置内网穿透。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它提供简单易用的穿透方案。详细的操作指南可以 [点击查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手教程。

### Docker 快速部署

VoceChat 提供了最简洁的 Docker 单命令部署方式，非常适合技术小白用户。

**第一步：打开你的 NAS 设备或任何支持 Docker 的系统**

进入 Docker 管理界面，选择'本地镜像'→'添加镜像'→'从 URL 添加'。

**第二步：添加镜像**

在镜像地址栏填入：

```
privoce/vocechat-server:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

**第三步：配置容器参数**

勾选'开机自动启动'后进入下一步。

**端口设置：**

容器内部端口：`3000`

主机端口：`3000`（如果你的系统中 3000 端口已被占用，可以修改为其他端口，比如 `3001`、`8080` 等，但容器内部端口保持 `3000` 不变）

**存储位置：**

VoceChat 会自动在容器内创建必要的数据目录。如果你需要持久化存储数据，建议在 NAS 上创建一个文件夹（例如 `/mnt/vocechat-data`），然后将其映射到容器内的 `/data` 目录。这样即使容器重启，你的数据也不会丢失。

**环境变量设置：**

VoceChat 基础版本无需配置特殊的环境变量，使用默认配置即可正常运行。如有高级需求，可参考官方文档进行自定义配置。

**第四步：启动完成**

创建成功后，打开浏览器访问 `http://你的NAS地址:3000`（如果修改了端口，请使用对应的端口号），即可看到 VoceChat 的登录界面。

首次访问时，你需要创建一个管理员账号，然后就可以开始使用私有聊天服务了。邀请你的朋友或团队成员加入，开始享受安全、私密的聊天体验吧！

本篇教程结束

---

原文：<https://www.passnat.com/posts/vocechat-private-deployment-nas-internal-network-penetration>
