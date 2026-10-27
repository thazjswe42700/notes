---
title: "飞牛 NAS 部署 drawio 流程图工具：浏览器画图、保存图稿并导出 PNG"
date: 2026-10-03
description: "飞牛 NAS 部署官方 draw.io，手动画流程图，下载可编辑图稿和 PNG 图片，并通过 PassNAT 插件在外面访问。"
canonical: https://www.passnat.com/posts/drawio-nas-flowchart-docker-guide
---

## 项目介绍

draw.io 是一个开源的浏览器画图工具，可以手动画流程图、网络拓扑和思维导图。本教程演示官方 jgraph/drawio 镜像，不是此前的 next-ai-draw-io AI 画图项目。

本期采用离线模式，图稿保存为当前设备上的 .drawio 文件，导出 PNG 图片用于文档或分享。NAS 提供编辑器网页；这套编排没有配置服务器图稿库或多人实时协作。

## 部署安装

我家是移动宽带，没有公网 IP，想在外面也能打开 NAS 上的画图工具，可以使用 [帕斯内网穿透](https://passnat.com)，在飞牛应用中心安装插件，并在插件内创建隧道。

## 一、准备编排

```yaml
services:
  drawio:
    image: jgraph/drawio:latest
    restart: unless-stopped
    ports:
      - "3440:8080"
```

镜像使用官方 jgraph/drawio:latest。端口 3440:8080 中，左边是 NAS 上的访问端口，右边是容器端口。若 3440 被其他服务占用，换左边，并在后面的隧道里填写相同端口。

## 二、在飞牛 Docker 中创建项目

打开 Docker，切到 Compose，新增项目，项目名填写 drawboard。在「我的文件」中选择一个空目录；来源选「创建 docker-compose.yml」，整段粘贴上面的编排。勾选「创建项目后立即启动」，确认后等待项目正在运行。

## 三、插件内创建隧道

在飞牛应用中心搜索 PassNAT 并安装，账号登录一次。打开插件，创建隧道：名称填 drawio，协议 HTTP，选可用节点，子域名自定。本地地址留默认，端口 3440。创建后打开详情，按最左侧电源启动；运行中时复制公网网址。

## 四、打开中文离线画布

在公网网址后面加上 /?offline=1&lang=zh，例如 http://你的子域名.odn.cc/?offline=1&lang=zh 。offline=1 禁用云盘存储选项，lang=zh 使用中文界面。无需翻译或 AI API key。

## 五、画图、保存和导出

从左侧通用图形拖一个矩形到画布，双击矩形输入「准备素材」；再拖一个矩形，输入「开始制作」。从第一个矩形的连接点拖向第二个矩形，生成连接箭头。

文件 → 另存为，保留 .drawio 格式，位置选择「下载」，点「确定」。之后可通过「文件 → 从...打开 → 设备」重新打开继续编辑。要分享图片，选择「文件 → 导出为 → PNG」，确认导出并保存到设备。

---

原文：<https://www.passnat.com/posts/drawio-nas-flowchart-docker-guide>


## 相关文章

- [自建 AI 画流程图：飞牛 NAS Docker 部署 next-ai-draw-io，一句中文生成 draw.io 图表并远程访问](ai-flowchart-next-ai-draw-io-nas-docker-deployment-intranet-penetration.md)
- [自建 changedetection.io：飞牛 NAS Docker 部署 + 内网穿透外网访问](changedetection-io-website-change-monitor-nas-docker-deployment-intranet-penetration.md)
- [Navidrome 飞牛 NAS 部署与公网听歌](navidrome-docker-private-music-server.md)
- [Excalidraw 白板：NAS Docker 部署 + 内网穿透](excalidraw-nas-docker-whiteboard-diagrams-intranet-penetration.md)
- [qBittorrent 飞牛 NAS 部署与远程下载](qbittorrent-nas-remote-bt-downloader-deploy-intranet-penetration.md)
