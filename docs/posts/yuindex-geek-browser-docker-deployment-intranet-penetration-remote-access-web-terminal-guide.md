---
title: "YuIndex极客浏览器主页Docker一键部署，内网穿透远程访问，Web终端命令操作完全指南"
date: 2025-10-16
description: "YuIndex是极客打造的浏览器主页，基于Vue3+Vite2+TypeScript构建。支持多平台搜索、快速跳转、待办事项、音乐播放等功能，仅需键盘命令即可高效操作。本文详解Docker快速部署方法、端口配置、数据持久化设置及常用命令用法，助你轻松搭建个性化主页。"
canonical: https://www.passnat.com/posts/yuindex-geek-browser-docker-deployment-intranet-penetration-remote-access-web-terminal-guide
---

YuIndex - 极客范儿的浏览器主页部署指南

## 项目介绍

YuIndex 是一个专为极客打造的浏览器主页，采用 Vue 3 + Vite 2 + TypeScript 构建。这不仅仅是一个漂亮的浏览器主页，更是一个功能强大的 Web 终端，让你可以通过输入命令的方式高效完成各种操作。

项目支持多平台搜索、快速网页跳转、空间管理（收藏夹）、待办事项、音乐播放、摸鱼小游戏等丰富功能。无需鼠标，仅通过键盘命令即可快速完成操作，极具 Linux 的味道。界面简洁炫酷，完全是极客的最爱。

无论你是想打造个性化主页、学习 Web 终端开发，还是想深入了解系统设计，YuIndex 都能满足你的需求。

项目地址：[点我跳转](https://github.com/liyupi/yuindex)

在线体验：http://yuindex.com

## 部署安装

如果你想在公网环境下随时随地访问自己部署的 YuIndex，建议配置内网穿透。推荐使用 [帕斯内网穿透](https://passnat.com)，先完成账号注册即可。具体的操作流程可以参考 [帕斯内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，几分钟内就能让你的本地应用在外网也能轻松访问。

### Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板，执行以下命令快速启动 YuIndex：

```bash
docker run -d \
  --name yuindex \
  -p 3000:3000 \
  -e NODE_ENV=production \
  yupi/yuindex:latest
```

等待镜像下载完成后，容器会自动启动。

### 详细配置说明

**端口映射**

宿主机端口：3000（可根据需要修改，如果 3000 端口被占用，可改为其他端口如 8080）

容器内端口：3000（保持不变，这是应用实际运行的端口）

如果需要修改宿主机端口，将命令中的 `-p 3000:3000` 改为 `-p 你的端口:3000` 即可。

**环境变量配置**（可选）

NODE\_ENV：运行环境，默认值为 `production`（生产环境）

其他配置保持默认即可，无需额外设置。

**存储持久化**（可选）

如果你需要保存配置信息和用户数据，建议添加数据卷挂载：

```bash
docker run -d \
  --name yuindex \
  -p 3000:3000 \
  -v /your/data/path:/app/data \
  -e NODE_ENV=production \
  yupi/yuindex:latest
```

将 `/your/data/path` 替换为你 NAS 或主机上的实际路径。

## 快速开始

部署完成后，在浏览器中访问 `http://你的IP:3000`，即可打开 YuIndex 主页。

首次使用建议尝试以下命令来熟悉功能：

baidu 程序员鱼皮 - 百度搜索

github yuindex - 快速跳转到 GitHub

goto yupi.icu - 快速打开网址

todo - 打开待办事项

music 坤坤 - 播放音乐

moyu - 摸鱼小游戏

help - 查看帮助信息

Ctrl + L - 清屏

Ctrl + O - 折叠/展开命令

更多命令用法，可以在终端中输入 `help 命令名` 查询。

## 总结

YuIndex 部署非常简单，一条 Docker 命令即可启动。无论你是想要一个个性化的浏览器主页，还是想学习 Web 终端和系统设计的精妙之处，YuIndex 都是一个绝佳的选择。快来体验这个极客范儿十足的项目吧！

本篇教程结束。

---

原文：<https://www.passnat.com/posts/yuindex-geek-browser-docker-deployment-intranet-penetration-remote-access-web-terminal-guide>


## 相关文章

- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [自托管即时通讯平台Stoat完整部署指南，内网穿透远程访问无公网限制](self-hosted-stoat-instant-messaging-deployment-guide-intranet-penetration-remote-access-no-public-ip.md)
- [免费在线虚拟钢琴演奏平台 AutoPiano NAS 极简部署 内网穿透远程弹奏](free-online-virtual-piano-autopiano-nas-simple-deployment-intranet-penetration-remote-playing.md)
- [Docker容器运行微信应用，Web远程访问与VNC连接，NAS一键部署搭建家庭微信服务器](docker-weixin-nas-vnc-remote-access.md)
