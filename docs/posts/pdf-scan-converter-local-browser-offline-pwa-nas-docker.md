---
title: "PDF文件扫描效果转换器，本地浏览器处理无隐私泄露，支持离线PWA应用，NAS Docker快速部署。"
date: 2025-10-16
description: "Look Scanned是一款轻量级前端PDF转换工具，能将文档转换成扫描件效果。采用Vue 3+TypeScript技术构建，支持离线使用和PWA功能。所有处理在浏览器本地进行，无需服务器，完全保护隐私。支持Docker快速部署到NAS设备，无需环境变量配置，即装即用。"
canonical: https://www.passnat.com/posts/pdf-scan-converter-local-browser-offline-pwa-nas-docker
---

## 项目介绍

**Look Scanned** 是一个轻量级的纯前端应用，能够将你的 PDF 文档转换成看起来像扫描件的效果。无需购置昂贵的扫描仪和打印机，只需几次点击就能实现专业的扫描文档效果。这个项目采用 Vue 3 + TypeScript + Vite 技术栈构建，具有完整的离线支持能力。

所有处理都在你的浏览器中进行，完全无需担心隐私泄露问题。支持 PWA 离线使用，即使没有网络连接也能正常工作。实时预览原文档和处理后的效果对比，支持所有现代浏览器和设备。项目采用纯静态文件部署，无需后端服务器支持。

unknown node[点我跳转](https://github.com/rwv/lookscanned.io)

## 部署安装

unknown node[帕斯内网穿透](https://passnat.com)unknown node[内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，轻松实现远程访问体验。

### 使用 Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板，点击**本地镜像** - **添加镜像** - **从 URL 添加**。

在镜像地址栏填入：

```
rwv/lookscanned.io:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

**容器配置说明：**

开机自动启动：勾选此项，确保容器在 NAS 重启后自动运行

端口映射：将容器内的 **3000 端口**映射到主机端口（建议保持为 3000，如遇端口占用可修改为其他端口如 3001、3002 等，但容器内端口需保持为 3000）

存储位置：此项目为纯前端应用，无需特殊的持久化存储配置

**环境变量设置：**

此项目无需配置环境变量，保持默认即可。

配置完成后点击启动，稍等片刻即可通过 `http://你的设备IP:3000` 访问应用。

部署至此完成！你现在可以在浏览器中打开 Look Scanned，轻松将任何 PDF 转换成扫描文档效果。所有处理都在本地进行，安全、快速、无隐忧。

---

原文：<https://www.passnat.com/posts/pdf-scan-converter-local-browser-offline-pwa-nas-docker>


## 相关文章

- [开箱即用 Markdown 笔记应用，多库协作实时编辑，NAS 极简部署 Many Notes 内网穿透远程访问](many-notes-markdown-notebook-collaborative-editing-nas-deployment.md)
- [自托管日常任务笔记管理器DailyNotes，Markdown编辑加密存储，Docker一键部署](daily-notes-self-hosted-markdown-encrypted-storage-docker.md)
- [Lookscanned：NAS Docker 部署，电子 PDF 一键变扫描件](lookscanned-nas-docker-pdf-to-scanned-image-intranet-penetration.md)
- [家常菜谱聚合平台来做菜，材料有限也能做美食，支持离线PWA，NAS一键Docker部署](home-cooking-recipes-pwa-nas-docker-deployment.md)
- [本地密码生成器PSWD，隐私优先TypeScript构建，支持Docker和NAS快速部署，内网穿透远程访问](local-password-generator-pswd-privacy-first-typescript-docker-nas-deployment-intranet-penetration-remote-access.md)
