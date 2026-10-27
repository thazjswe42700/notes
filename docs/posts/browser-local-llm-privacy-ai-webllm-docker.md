---
title: "浏览器本地运行大型语言模型 完全隐私AI对话 WebLLM Chat Docker一键部署"
date: 2025-10-16
description: "WebLLM Chat是完全运行在浏览器中的私密AI对话平台，融合WebLLM强大能力与NextChat友好界面。利用WebGPU技术直接在浏览器本地运行大型语言模型，所有数据处理在本地进行，永不上传服务器。支持多种模型、图像识别、离线使用，提供完全隐私保护的开源AI聊天解决方案。"
canonical: https://www.passnat.com/posts/browser-local-llm-privacy-ai-webllm-docker
---

## 项目介绍

WebLLM Chat 是一款完全运行在浏览器中的私密AI对话平台。它融合了 WebLLM 的强大后端能力与 NextChat 友好的用户界面，通过 WebGPU 技术直接在你的浏览器中本地运行大型语言模型，无需依赖任何云服务或服务器处理。

这是一个开源的、完全隐私保护的AI聊天解决方案。所有对话数据都在你的本地设备上处理，永远不会上传到任何服务器，让你可以放心地与AI进行交互。支持多种内置模型、图像识别、离线使用等功能，是自建AI助手的绝佳选择。

[点我跳转](https://github.com/mlc-ai/web-llm-chat)

项目地址：

## 核心特性

🧠 浏览器原生AI：利用WebGPU加速，大型语言模型直接在浏览器中运行

🔒 完全隐私：所有数据处理都在本地进行，对话内容永远不离开你的电脑

📱 离线可用：初次下载后可完全离线运行，无需网络连接

🖼️ 图像识别：支持上传图片进行视觉识别和分析

🎨 美观界面：支持深色模式、Markdown渲染、响应式设计

⚙️ 自定义模型：通过MLC-LLM连接本地自定义模型

🔓 完全开源：可自由定制和部署

## 部署安装

如需在外网也能访问该项目，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的跨平台部署指南可以 [点击查看](https://www.passnat.com/posts/p)。

### 使用 Docker 快速部署

打开你的 NAS 设备或本地服务器的 Docker 管理界面。

### 第一步：添加镜像

点击'本地镜像' - '添加镜像' - '从 URL 添加'，在镜像地址栏填入：

```
mlcai/web-llm-chat:latest
```

等待镜像下载完成。

### 第二步：启动容器

下载完成后点击启动按钮，进入容器配置界面。

勾选'开机自动启动'确保服务重启后自动运行。

### 第三步：端口配置

在端口映射设置中，将容器端口 **3000** 映射到你想要的宿主机端口（默认保持 3000 即可）。如果宿主机的 3000 端口已被占用，可以改为其他端口，如 3001、3002 等，容器内部端口保持不变。

### 第四步：环境变量配置（可选）

如需配置代理访问，可添加以下环境变量：

环境变量 | 说明 | 示例值
PROXY\_URL | 代理服务器地址（可选） | http://localhost:7890

如果代理需要身份验证，使用格式：*http://127.0.0.1:7890 user pass*

其他环境变量保持默认即可，无需特殊配置。

### 第五步：完成部署

点击下一步启动容器。启动成功后，在浏览器中访问：

```
http://你的IP:3000
```

即可进入 WebLLM Chat 界面，开始享受本地化的AI对话体验。

## 使用建议

首次使用时会下载模型文件，请耐心等待

建议使用现代浏览器（Chrome、Edge、Firefox 最新版本）以获得最佳体验

如需使用自定义模型，可通过 MLC-LLM REST API 进行配置

所有对话数据都存储在本地，可放心使用

## 总结

WebLLM Chat 为你提供了一个完全私密、无云依赖的AI对话平台。通过简单的 Docker 部署，你就可以拥有一个属于自己的、完全离线可用的智能助手。无论是个人使用还是家庭部署，都能获得出色的隐私保护和使用体验。

本篇教程结束

---

原文：<https://www.passnat.com/posts/browser-local-llm-privacy-ai-webllm-docker>


## 相关文章

- [自然语言SQL对话工具，AI驱动数据库客户端，支持多数据库NAS快速部署](natural-language-sql-chat-ai-database-client-multi-database-nas-deployment.md)
- [免费私密本地AI搜索引擎，无需API密钥NAS一键部署，多LLM模型聚合智能问答](free-private-local-ai-search-engine-no-api-key-nas-one-click-deploy-multi-llm-aggregation-qa.md)
- [本地私有AI聊天助手 Serge 离线运行LLM 无需API密钥 NAS极简部署内网穿透远程访问](serge-offline-llm-chat-assistant-nas-deployment.md)
- [浏览器WebRTC视频通话应用Call-Me，NAS一键部署，支持屏幕共享与内网穿透远程通话](browser-webrtc-video-call-app-call-me-nas-one-click-deployment-screen-sharing-intranet-penetration-remote-calling.md)
- [深度网络研究工具本地部署指南，NAS一键启动Deep Research Web UI，支持多AI提供商](deep-network-research-tool-local-deployment-guide-nas-one-click-launch-deep-research-web-ui-multi-ai-providers.md)
