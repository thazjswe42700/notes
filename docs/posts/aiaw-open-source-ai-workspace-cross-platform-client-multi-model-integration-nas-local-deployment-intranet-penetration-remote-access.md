---
title: "开源AI工作空间AIaW：跨平台客户端，集成多家大模型，NAS本地部署内网穿透远程访问"
date: 2025-10-16
description: "AIaW是一款跨平台AI客户端应用，集成OpenAI、Anthropic、Google等多家AI服务商。提供Windows、Linux、Mac、Android及Web支持，支持本地离线使用和云端同步。内置MCP协议、网页搜索、代码生成等功能，采用工作区分类管理对话，配备活跃插件市场。本文介绍AIaW的核心功能和Docker快速部署方法，助力用户轻松搭建私有AI工作空间。"
canonical: https://www.passnat.com/posts/aiaw-open-source-ai-workspace-cross-platform-client-multi-model-integration-nas-local-deployment-intranet-penetration-remote-access
---

## 项目介绍

AIaW（AI as Workspace）是一款优雅且功能强大的AI客户端应用。它提供了跨平台的一致体验，支持Windows、Linux、Mac OS、Android以及Web（PWA）等多个平台。该项目集成了OpenAI、Anthropic、Google、DeepSeek、xAI、Azure等多家主流AI服务商，让用户能够在统一的界面中无缝切换不同的AI模型。

AIaW不仅仅是一个对话工具，它更像是一个完整的AI工作空间。用户可以创建多个独立的工作区来分类管理不同主题的对话，支持文件夹嵌套组织，每个工作区内还可以配置多个专属助手。项目采用本地优先的数据存储策略，确保即使离线也能正常使用，登录后还支持跨设备云端同步，让你的工作流无处不在。

该应用还内置了MCP协议支持、网页搜索、代码生成、图片生成等丰富功能，以及一个活跃的插件市场。无论是开发者还是普通用户，都能在AIaW中找到适合自己的使用方式。

项目地址：[点我跳转](https://github.com/NitroRCr/AIaW)

## 部署安装

如果你想在办公室、家里或任何地方都能随时访问这个AI工作空间，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的配置步骤可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台快速上手指南。

### 快速部署（Docker方式）

打开你的NAS设备或服务器的Docker管理面板，点击'本地镜像'-'添加镜像'-'从URL添加'。

在镜像地址栏填入：nitrorrcr/aiaw:latest

等待镜像下载完成后点击启动按钮。

端口配置

在容器启动配置中，需要进行以下端口映射设置：

容器内部端口：5173（应用运行端口）

主机映射端口：5173（如无冲突可保持不变，若端口被占用可自行修改为其他端口如 8080、3000 等）

存储位置

AIaW会在本地存储你的对话记录、工作区配置等数据。建议在NAS上创建一个专用文件夹（如 /mnt/data/aiaw）并将其映射到容器内的 /app/data 目录，这样即使容器重启也不会丢失数据。

环境变量设置

AIaW的基础功能无需额外的环境变量配置，开箱即用。如果你需要配置特定的AI服务商API密钥或其他高级选项，可以在启动后通过Web界面进行配置。

勾选'开机自动启动'后，点击启动即可。

访问应用

部署完成后，在浏览器中访问 http://你的设备IP:5173 即可进入AIaW工作空间。首次进入会看到优雅的界面和完整的功能菜单，可以立即开始配置你的AI助手。

本篇教程结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/aiaw-open-source-ai-workspace-cross-platform-client-multi-model-integration-nas-local-deployment-intranet-penetration-remote-access>
