---
title: "浏览器P2P文件传输工具FastSend，WebRTC加密传输，NAS极简Docker部署，局域网极速秒传无限制"
date: 2025-10-16
description: "FastSend是基于WebRTC技术的点对点文件传输工具，支持浏览器快速安全传输文件。采用端到端加密，无需上传到第三方服务器。Vue.js+Nuxt3构建，支持中英文双语和PWA安装。局域网内传输速度最快，提供Docker一键部署，适合NAS设备使用，保护您的数据隐私安全。"
canonical: https://www.passnat.com/posts/fastend-p2p-file-transfer-webrtc-nas-docker
---

## 项目介绍

FastSend 是一个基于 WebRTC 技术的点对点文件传输工具，让你可以通过浏览器快速、安全地传输文件和文件夹。相比传统的文件传输方式，FastSend 采用端到端的加密传输，确保你的数据安全可靠。项目采用 Vue.js + Nuxt3 + TypeScript 技术栈构建，界面简洁易用，支持中英文双语，还支持 PWA 轻量安装，让你可以像使用原生应用一样体验文件传输。

最棒的是，FastSend 完全基于 WebRTC 技术实现点对点传输，在同一局域网内能获得最快的传输速度，无需担心文件上传到第三方服务器的隐私问题。

项目地址：[点我跳转](https://github.com/ShouChenICU/FastSend)

## 部署安装

如果你需要在外网也能访问 FastSend，建议配置内网穿透工具，这样即使在公司或其他地方也能随时访问家里的文件传输服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，具体的配置方法可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台上手攻略。

FastSend 提供了最简便的 Docker 单命令启动方式，即使是技术小白也能轻松部署。

### 使用 Docker 快速启动

打开飞牛 NAS 或其他 NAS 设备的管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入官方镜像地址：

```
shouchenicu/fastsend:0.6.0
```

等待镜像下载完成后，点击 **启动** 按钮启动容器。

勾选 **开机自动启动** 后进入下一步。

**端口设置**：这里设置容器映射的端口。默认情况下，容器内部运行在 3000 端口。如果你的 NAS 上 3000 端口已被占用，可以修改为其他端口号（如 3001、3002 等），但容器内部端口保持 3000 不变。

**存储位置**：FastSend 本身无需持久化存储，不需要创建额外的文件夹映射。

**环境变量**：此项目无需配置环境变量，保持默认即可。

设置完成后点击 **启动** 按钮，容器即可运行。

创建成功后，在浏览器中访问 `http://NAS的IP地址:3000`（如果修改了端口号，请使用对应的端口），就能看到 FastSend 的界面了。

现在你就可以开始使用 FastSend 进行快速的文件传输了。在同一局域网内，两台设备可以通过扫描二维码或输入连接码快速建立连接，然后就能以最快的速度传输文件。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/fastend-p2p-file-transfer-webrtc-nas-docker>


## 相关文章

- [功能强大IP诊断工具MyIP Docker部署指南内网穿透远程访问配置](powerful-ip-diagnostic-tool-myip-docker-deployment-guide-intranet-penetration-remote-access-configuration.md)
- [家常菜谱聚合平台来做菜，材料有限也能做美食，支持离线PWA，NAS一键Docker部署](home-cooking-recipes-pwa-nas-docker-deployment.md)
- [自托管联邦即时通讯Databag部署指南，树莓派轻量级私密通讯解决方案教程](self-hosted-databag-federated-messaging-deployment-guide-raspberry-pi-lightweight-private-communication.md)
- [浏览器WebRTC视频通话应用Call-Me，NAS一键部署，支持屏幕共享与内网穿透远程通话](browser-webrtc-video-call-app-call-me-nas-one-click-deployment-screen-sharing-intranet-penetration-remote-calling.md)
- [自托管私人相册HomeGallery，AI智能识别，NAS极简部署，内网穿透远程访问](home-gallery-private-photos-ai-recognition-nas-deployment-remote-access.md)
