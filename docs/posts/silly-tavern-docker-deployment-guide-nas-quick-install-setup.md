---
title: "本地AI交互平台SillyTavern Docker部署指南，NAS快速安装配置教程"
date: 2025-10-16
description: "SillyTavern是功能强大的本地化AI交互平台，支持KoboldAI、OpenAI、Claude等多种大语言模型API接入。集成图像生成、文本转语音、视觉小说模式等丰富功能，完全本地运行保护隐私。本文详细介绍SillyTavern的Docker快速部署方法，包括飞牛NAS容器配置、端口映射设置和持久化存储配置，帮助AI爱好者轻松搭建个人AI交互平台。"
canonical: https://www.passnat.com/posts/silly-tavern-docker-deployment-guide-nas-quick-install-setup
---

## 项目介绍

SillyTavern 是一款功能强大的本地化AI交互平台，为广大AI爱好者提供了统一的用户界面。它支持多种大语言模型API接入（包括KoboldAI、Horde、NovelAI、OpenAI、Claude、Mistral等），集成了图像生成、文本转语音、视觉小说模式等丰富功能。

这是一个从2023年2月开始独立开发的项目，已经拥有超过200位贡献者，成为了AI爱好者的首选工具。SillyTavern完全本地化运行，无需依赖任何在线服务，保护你的隐私安全。无论你是AI初学者还是资深玩家，都能在这个平台找到属于自己的乐趣。

[点我跳转](https://github.com/SillyTavern/SillyTavern)

项目地址：

## 部署安装

若想在外网访问本地部署的SillyTavern，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先完成账号注册以备后用。详细的操作指南可参考 [全平台快速上手教程](https://www.passnat.com/posts/p)，轻松实现随时随地访问。

### Docker 快速部署

打开你的飞牛NAS或其他支持Docker的NAS设备

点击 **本地镜像** - **添加镜像** - **从URL添加**

在镜像地址栏填入：`sillytavern:latest`

等待镜像下载完成后，点击启动按钮启动容器

**勾选开机自动启动**，然后进入下一步

**端口映射设置**：

容器内端口：`8000`（这是SillyTavern的默认服务端口）

主机端口：`8000`（如果你的NAS上8000端口已被占用，可改为其他端口如8001、8002等，但容器内端口需保持为8000）

**存储位置**：

建议在NAS上创建一个文件夹（如 `/data/sillytavern`）用于存储角色数据、聊天记录等持久化文件

将此文件夹映射到容器内的 `/home/node/app/data` 路径

**环境变量设置**：

**必须设置**：

`NODE\_ENV`: `production`（生产环境运行）

**可选设置**（保持默认即可）：

`PORT`: `8000`（服务端口，默认值）

其他配置保持默认，直接下一步启动容器即可

部署完成后，在浏览器中访问 `http://你的NAS地址:8000` 就能开始使用SillyTavern了。首次使用时会提示你配置AI模型API密钥，按照界面提示逐步配置即可。

本篇教程结束

---

原文：<https://www.passnat.com/posts/silly-tavern-docker-deployment-guide-nas-quick-install-setup>
