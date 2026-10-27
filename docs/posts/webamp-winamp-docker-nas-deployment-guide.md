---
title: "Webamp网页版Winamp音乐播放器Docker部署完整指南，NAS一键启动享受怀旧音乐体验"
date: 2025-10-16
description: "Webamp是用HTML5和JavaScript打造的Winamp音乐播放器网页版,完整支持皮肤导入。这款怀旧应用将经典Winamp重现在现代浏览器中,支持自定义皮肤、均衡器预设、播放列表管理等功能。已被互联网档案馆、TechCrunch等知名媒体采用报道。支持Docker快速部署,轻松在NAS或家庭网络中搭建个人音乐播放器。"
canonical: https://www.passnat.com/posts/webamp-winamp-docker-nas-deployment-guide
---

## 项目介绍

Webamp 是一款用 HTML5 和 JavaScript 打造的 Winamp 音乐播放器网页版，完整支持 Winamp 皮肤导入。这是一个充满怀旧情怀的项目，将经典的 Winamp 播放器重新呈现在现代浏览器中。

无论你是 Winamp 的忠实粉丝，还是想要体验这款传奇音乐播放器的新生代用户，Webamp 都能为你提供完整的播放体验。它支持自定义皮肤、均衡器预设、播放列表管理等功能，让你能够像使用原版 Winamp 一样享受音乐。

项目已被互联网档案馆（Internet Archive）、Winampify.io 等知名网站采用，并在 TechCrunch、Motherboard、Gizmodo 等多家媒体报道过。

项目地址：[点我跳转](https://github.com/captbaritone/webamp)

## 部署安装

如果你想在家庭网络外也能访问 Webamp，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地使用你的个人音乐播放器了。具体配置步骤可以参考 [内网穿透全平台快速指南](https://www.passnat.com/posts/p)。

### 使用 Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板，点击'本地镜像'→'添加镜像'→'从 URL 添加'。

在镜像地址栏填入：captbaritone/webamp:latest

等待镜像下载完成后，点击启动按钮启动容器。

**端口配置**

在容器配置中设置端口映射。Webamp 默认运行在容器内的 **3000 端口**，你可以将其映射到主机的任意端口（如 8080、8888 等）。如果遇到端口冲突提示，只需修改主机端口号即可，容器内端口保持 3000 不变。

**存储配置**

Webamp 本身无需特殊的持久化存储目录，所有配置都在浏览器本地存储中。如果你需要保存播放列表或其他数据，建议创建一个数据目录并挂载到容器中以备后用。

**环境变量设置**

Webamp 无需特殊的环境变量配置，使用默认设置即可正常运行。直接启动容器就能使用。

**开机自启**

勾选'开机自动启动'选项，这样 NAS 重启后容器会自动启动，无需手动干预。

### 访问应用

部署完成后，在浏览器中访问：http://你的NAS地址:3000

稍等片刻，经典的 Winamp 播放器界面就会出现在你的眼前。你可以上传本地音乐文件、导入 Winamp 皮肤、调整均衡器，尽情享受音乐体验。

本篇教程到此结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/webamp-winamp-docker-nas-deployment-guide>


## 相关文章

- [Win11React网页版Windows11桌面一键Docker部署，浏览器在线体验完整系统界面](win11-react-docker-browser-desktop.md)
- [Tellor轻量级任务看板应用自托管部署教程，Docker快速安装配置指南](tellor-lightweight-task-board-self-hosted-deployment-docker-installation-guide.md)
- [自建远程文件浏览器Mikochi，极简部署NAS文件管理，支持内网穿透外网访问](mikochi-remote-file-browser-nas-file-management-intranet-penetration.md)
- [Hugo极速静态网站生成器NAS部署指南,Go语言编写秒速渲染,内网穿透远程访问](hugo-nas-deployment-guide-go-fast-rendering-remote-access.md)
- [个人知识库TiddlyWiki5 Docker极简部署NAS无公网内网穿透远程访问](personal-knowledge-base-tiddlywiki5-docker-deployment-nas-intranet-tunneling-remote-access.md)
