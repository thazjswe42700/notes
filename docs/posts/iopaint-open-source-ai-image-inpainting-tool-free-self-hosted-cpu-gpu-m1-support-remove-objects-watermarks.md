---
title: "IOPaint开源AI图像修复工具，免费自托管，CPU/GPU/M1支持，智能去物体移除水印"
date: 2025-10-16
description: "免费开源AI图像修复工具IOPaint，支持物体移除、图像扩展、背景移除等功能。集成LaMa、PowerPaint等多种AI模型，支持CPU、GPU、Apple Silicon运行。提供Web UI界面，无需付费软件，专业级图像处理能力轻松获得。Docker快速部署。"
canonical: https://www.passnat.com/posts/iopaint-open-source-ai-image-inpainting-tool-free-self-hosted-cpu-gpu-m1-support-remove-objects-watermarks
---

## 项目介绍

IOPaint 是一款完全免费且开源的图像修复与扩展工具，采用最先进的 AI 模型驱动。它完全自托管，支持 CPU、GPU 以及 Apple Silicon 等多种硬件平台。无论你想要移除照片中的不需要的物体、修复缺陷、去除水印，还是进行图像扩展和创意编辑，IOPaint 都能轻松胜任。

项目提供了功能强大的 Web UI，集成了多种 AI 模型，包括 LaMa（擦除模型）、PowerPaint（物体替换）、AnyText（文字绘制）等。此外还支持丰富的插件系统，如 Segment Anything 智能分割、RemoveBG 背景移除、RealESRGAN 超分辨率、GFPGAN 面部修复等功能，让你的图像编辑工作事半功倍。

无需购买昂贵的商业软件，IOPaint 让每个人都能享受到专业级的 AI 图像处理能力。

项目地址：[点我跳转](https://github.com/Sanster/IOPaint)

## 部署安装

如果你想在外网也能访问到这个项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它支持全平台使用。具体的详细配置步骤可以参考 [这份上手指南](https://www.passnat.com/posts/p)，能帮你快速掌握内网穿透的使用方法。

### 使用 Docker 快速启动

打开你的 NAS 设备或其他支持 Docker 的系统，进入 Docker 管理界面。

点击**本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
ghcr.io/sanster/iopaint:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

### 端口映射设置

在容器配置中，需要进行端口映射。IOPaint 默认使用 **8080 端口**。建议将容器内的 8080 端口映射到宿主机的 8080 端口（或其他未被占用的端口）。如果 8080 端口已被占用，你可以修改宿主机端口为其他数字，比如 8081、8888 等，但容器内部端口保持 8080 不变。

### 存储配置

IOPaint 需要存储模型文件和处理过的图像。建议在 NAS 上创建一个专用文件夹（比如 `/iopaint/models` 和 `/iopaint/output`），然后将这些目录映射到容器内的对应位置：

容器内模型存储目录：`/root/.cache/huggingface`

容器内输出目录：`/app/output`

### 环境变量设置

IOPaint 提供了以下可选的环境变量配置：

必须设置

`DEVICE`：计算设备选择，可选值为 `cpu`、`cuda`、`mps`（Apple Silicon），默认为 `cpu`

可选设置

`MODEL`：启动时加载的默认模型，默认为 `lama`（推荐用于物体移除）

`PORT`：服务运行端口，默认为 `8080`

`ENABLE\_INTERACTIVE\_SEG`：是否启用智能分割插件，默认为 `false`

`ENABLE\_REMOVE\_BG`：是否启用背景移除插件，默认为 `false`

如果你使用的是 CPU 运行，可以不设置任何环境变量，直接启动即可。如果你有 NVIDIA GPU，建议将 `DEVICE` 设置为 `cuda` 以获得更快的处理速度。

配置完成后，点击启动容器。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:8080
```

比如你的 NAS IP 是 `192.168.1.100`，那就访问 `http://192.168.1.100:8080`，就能看到 IOPaint 的 Web 界面了。

到这里，IOPaint 就已经成功部署完成，你可以开始上传图片进行 AI 修复和编辑了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/iopaint-open-source-ai-image-inpainting-tool-free-self-hosted-cpu-gpu-m1-support-remove-objects-watermarks>


## 相关文章

- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [开源绘画板Paint Board在线创作 NAS部署内网穿透远程访问](open-source-paint-board-online-creation-nas-deployment-intranet-penetration-remote-access.md)
- [哪吒监控自建服务器探针：飞牛 NAS Docker 部署 + 内网穿透外网访问](nezha-server-monitoring-nas-docker-deployment-intranet-penetration.md)
- [开源AI工作空间AIaW：跨平台客户端，集成多家大模型，NAS本地部署内网穿透远程访问](aiaw-open-source-ai-workspace-cross-platform-client-multi-model-integration-nas-local-deployment-intranet-penetration-remote-access.md)
- [免费私密本地AI搜索引擎，无需API密钥NAS一键部署，多LLM模型聚合智能问答](free-private-local-ai-search-engine-no-api-key-nas-one-click-deploy-multi-llm-aggregation-qa.md)
