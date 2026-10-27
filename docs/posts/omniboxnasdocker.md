---
title: "自建OmniBox影视综合站：一站式观影平台部署指南，NAS一键启动Docker快速搭建多源聚合系统，内网穿透远程访问"
date: 2025-11-14
description: "OmniBox影视综合站，解决资源分散查找难题。多源聚合采集站、网盘、IPTV及主流直播平台。Docker容器化部署，支持群晖、飞牛等NAS一键安装。跨平台观看，支持弹幕互动和4K超清播放。配置简单，打造便捷私有化观影平台。"
canonical: https://www.passnat.com/posts/omniboxnasdocker
---

## 项目介绍

OmniBox 是 一个功能强大的影视综合站平台，旨在解决影视资源分散、查找困难的痛点，为用户提供一站式观影解决方案。该平台支持多源聚合，整合了采集站、网盘资源、IPTV 直播源以及 6 大主流直播平台，无论是付费会员还是免费资源用户都能获得优质体验。

OmniBox 采用 Docker 容器化部署，可在群晖等 NAS 设备上一键安装启动，配置简单便捷。平台支持跨平台观看，提供网页端、移动端、TV端等多种访问方式，所有客户端均支持弹幕互动功能，最高可播放4K超高清视频。

相比传统的 Emby、TVBox 等工具，OmniBox 配置更简单，使用更友好。通过整合 PanSou 网盘搜索、IPTV 直播等功能模块，打造了一个稳定、便捷、功能丰富的私有化观影平台，让您告别繁琐的应用切换和广告困扰。

项目地址：[点我跳转](https://hub.docker.com/r/lampon/omnibox)

## 部署安装

如果你想在办公室或家里随时随地访问这个项目，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)

来实现远程访问，先注册账号备用。具体的详细操作步骤可以参考 [这份完整的上手攻略](https://www.passnat.com/posts/p)

，里面有针对各个平台的详细说明。

### 部署教程：

本片文章使用的是飞牛 NAS ，群晖部署步骤可参考上方[完整的上手攻略](https://www.passnat.com/posts/p)。

打开飞牛 NAS 管理界面-容器-镜像仓库，搜索 OmniBox 并且下载。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-11-14_17-44-48.png%3F2025-11-14T09%3A49%3A48.328Z&w=3840&q=80)

回到本地镜像，创建一个容器。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-11-14_17-56-42.png%3F2025-11-14T09%3A57%3A35.855Z&w=3840&q=80)

前面的 7023 可以修改成其他未占用的端口，下方的装载位置改成 /app/data，修改完成后下一步，创建容器。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-11-14_18-05-14.png%3F2025-11-14T10%3A05%3A43.924Z&w=3840&q=80)

这样就部署完成了。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/omniboxnasdocker>


## 相关文章

- [网页版IPTV播放器Streamdock简易部署指南，无客户端浏览器直播观看体验](streamdock-web-iptv-player-deployment-guide-browser-live-streaming.md)
- [自建IPTV直播平台无需公网，Docker快速部署秒播体验，内网穿透随处访问管理](self-hosted-iptv-live-streaming-platform-docker-deployment-no-public-ip-fast-playback-internal-network-penetration.md)
- [IPTV m3u直播源聚合工具，本地部署智能检测优化，NAS内网快速搭建稳定播放列表](iptv-m3u-live-source-aggregation-tool-local-deployment-smart-detection-optimization-nas-intranet-fast-setup-stable-playlist.md)
- [自动聚合优质IPTV直播源，支持NAS极简部署与内网穿透远程访问](auto-aggregate-quality-iptv-live-sources-nas-simple-deployment-intranet-penetration-remote-access.md)
- [统一网盘聚合管理工具PanIndex，多云存储跨平台部署，内网穿透远程访问无限资源](panindex-unified-cloud-storage-aggregation-tool-multi-cloud-cross-platform-deployment.md)
