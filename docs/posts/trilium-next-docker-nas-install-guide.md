---
title: "TriliumNext 个人知识库：飞牛 NAS Docker 部署 + 内网穿透外网访问"
date: 2025-10-16
description: "TriliumNext 是自己部署的层级笔记本，笔记挂成一棵树，一个容器就能跑。本文在飞牛 NAS 上用 Docker Compose 部署 TriliumNext，用帕斯内网穿透开放到公网，再把一篇笔记分享成不用登录就能看的公开网页。含首启设密码、镜像改名两处必看项。"
canonical: https://www.passnat.com/posts/trilium-next-docker-nas-install-guide
---

## 项目介绍

TriliumNext 是一个自己部署的笔记本 / 个人知识库。笔记挂成一棵树，想分几层就分几层，同一篇笔记还能同时挂在好几个地方；编辑器是所见即所得的，表格、代码块、图片都能直接写。它是原来 Trilium Notes 的社区延续版本，现在由社区维护。

这一篇重点讲它一个很实用的功能：**把一篇笔记分享成公开网页**。打开分享之后，这篇笔记就有了一个不用登录就能看的链接，发给朋友，对方点开就是一个干净的只读网页。前提是这个链接从外面打得开 —— 这就是为什么装完之后要把它开放到公网。

它也够轻：一个容器就跑起来，笔记库是一个文件型数据库，所有笔记都在 NAS 的一个文件夹里。

项目地址：[点我跳转](https://github.com/TriliumNext/Trilium)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装 TriliumNext，装完再用 [帕斯内网穿透](https://passnat.com) 把它开放到公网。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 trilium，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去。

```yaml
services:
  trilium:
    image: triliumnext/trilium:latest
    container_name: trilium
    restart: unless-stopped
    environment:
      - TRILIUM_DATA_DIR=/home/node/trilium-data
    ports:
      - "3433:8080"
    volumes:
      - ./data:/home/node/trilium-data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
```

这份编排要认三处：

冒号左边的 3433 是 NAS 上对外的端口，一会儿浏览器访问、建隧道填的都是它；冒号右边的 8080 是容器里自己监听的端口，别改。

./data 是笔记库，所有笔记、配置和自动备份都在这个文件夹里。备份就是把这个文件夹拷走；不挂它的话容器一删，笔记就没了。

最后两行把 NAS 的时区挂进容器，让笔记的创建时间、修改时间跟你这边对得上。

**关于镜像**：用 triliumnext/trilium。网上一些老教程写的是 triliumnext/notes，那是项目改名之前的镜像名，已经不再更新了，别拿它。

勾上「创建项目后立即启动」，点确认。镜像一百七十来兆，拉完等一小会儿就起来了。

### 二、先建隧道，但先别通电

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起，接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填编排里那个 3433。

🔴 **建好之后先别按电源。** 原因在下一步。

### 三、局域网走完设置向导、设好密码，再通电

TriliumNext 没有出厂密码，**第一次打开时会让你自己设一个**。反过来说，在你设密码之前，**谁先打开这个页面，谁就能来设** —— 所以一定要先在家里的网里把它设好，再把它放到公网上。

用局域网地址（NAS 的 IP 加 3433 端口）打开，向导一共几步：语言选「简体中文」→ 选「新建知识库」→ 选「空白」（想先看看示例可以选「包含演示内容」）→ 设置密码，输两遍，提交。之后会跳到登录页，用这个密码登进去就行。

设好之后回插件里按一下电源，隧道状态转成运行中，详情里那一行就是系统分给你的公网地址。

### 四、在外面写笔记

用公网地址打开、登进去，就跟在家里一样用。左边是笔记树，在某篇笔记上右键「插入子笔记」就能往下挂一层；标题、正文直接写，自动保存。

### 五、一篇笔记分享成公开网页

这是这套东西最好玩的地方。打开想分享的那篇笔记，点编辑区右上角的「⋯」，把「已分享」打开。标题旁边会多出一个「公开共享」的标记，点它能「复制链接到剪贴板」，或者直接「在浏览器中打开链接」。

这个链接不用登录就能看：发给朋友，对方打开就是一个干净的网页，只能看、改不了你的笔记。想收回的话，在同一个菜单里点「取消共享」，链接就失效了。

⚠ 链接用的是你打开笔记时的那个地址 —— 在公网地址上操作，复制出来的就是公网链接，朋友在外面才打得开。

### 几点说明

**数据就是一个文件夹。** 笔记库在 ./data 里，想备份就拷走，换台机器就把目录挪过去。

**不需要任何外部账号或密钥。** 登录密码是你在向导里自己设的，编排里没有任何口令。

**分享出去的只是那一篇和它下面的子笔记**，其他笔记外人打开是 404，看不到。

---

原文：<https://www.passnat.com/posts/trilium-next-docker-nas-install-guide>


## 相关文章

- [DailyTxT 加密日记：飞牛 NAS Docker 部署 + 内网穿透外网访问](private-diary-encryption-nas-local-deployment-dailytxt-intranet-penetration-remote-access.md)
- [哪吒监控自建服务器探针：飞牛 NAS Docker 部署 + 内网穿透外网访问](nezha-server-monitoring-nas-docker-deployment-intranet-penetration.md)
- [Moments 自建朋友圈：飞牛 NAS Docker 部署 + 内网穿透外网访问](moments-self-hosted-private-timeline-nas-docker-deployment-intranet-penetration.md)
- [自托管个人知识库Karakeep，AI智能标签书签管理，NAS极简部署内网穿透远程访问](karakeep-ai-bookmark-manager-nas-deployment.md)
- [Lychee 自建私人相册：飞牛 NAS Docker 部署 + 内网穿透外网访问](lychee-nas-self-hosted-photo-album-share-link-password.md)
