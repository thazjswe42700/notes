---
title: "Jellyfin 家庭影院：飞牛 NAS Docker 部署 + 内网穿透，出门手机接着看"
date: 2026-10-04
description: "Jellyfin 把电影剧集整理成自己的流媒体库：飞牛 NAS Docker Compose 一段编排装好，PassNAT 开放到公网，出门手机接着看，播放进度自动同步。"
canonical: https://www.passnat.com/posts/jellyfin-nas-media-server-private-cinema
---

## 项目介绍

Jellyfin 是一款开源免费的媒体系统，把你的电影和剧集整理成自己的流媒体库：海报墙、点开就播，手机、平板、电视、浏览器都能看，播放进度还会在各设备之间自动同步。媒体文件全部存在自己的 NAS 上，不经过任何第三方的云。

它是 Emby 和 Plex 的开源替代品，由社区驱动，库怎么建、给谁看，都由你自己决定。[GitHub 项目地址](https://github.com/jellyfin/jellyfin)

## 部署安装

完整编排文件和图文步骤都在这篇文章里，建议先存下来再看。如果你希望在外面也能看家里的电影，需要用 [帕斯内网穿透](https://passnat.com) 把 Jellyfin 开放到公网，注册和客户端下载都在官网。

### 使用 Docker Compose 部署

打开飞牛的 Docker 应用，切到 Compose，点右上角「新增项目」，名字填 jellyfin，路径选一个空的文件夹，来源选「创建 docker-compose.yml」，把下面这段编排整个贴进去：

```yaml
services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    network_mode: bridge
    ports:
      - "8096:8096"
    volumes:
      - ./config:/config
      - ./cache:/cache
      - ./media:/media
    environment:
      - TZ=Asia/Shanghai
    restart: unless-stopped
```

从上往下认两处：8096 是对外端口，建隧道的时候要填；./media 就是放电影的文件夹，config 和 cache 会自动建在你选的目录里。确认时勾上「创建项目后立即启动」，第一次要下载镜像，等容器跑起来。

### 放入电影文件

打开飞牛的文件管理，把电影文件放进你建项目时选的那个目录下的 media 文件夹，一部电影一个文件夹，命名带上片名和年份（比如 Sintel (2010)），Jellyfin 就能自动配上海报和简介。放多少部都行。

### 初始化 Jellyfin

浏览器打开 http://NAS的IP:8096，第一次会进初始化向导：选语言，然后创建管理员账号，账号口令自己设。建议在局域网里先把这些配好。

### 添加媒体库

进控制台 → 媒体库 → 添加媒体库：内容类型选「电影」，文件夹指到 media，保存后自动扫描，海报墙就出来了。

### 用 PassNAT 开放到公网

在飞牛自带的应用中心里搜 PassNAT，装上这个内网穿透插件，账号登一次。点创建隧道：名字随便起，端口填 8096，本地地址留默认的 127.0.0.1，点创建，再按一下电源，状态变成运行中之后，详情里就是你的公网地址。家里没有公网 IP 也能用——我家是移动宽带，就是这么看的。

第一次从公网地址打开 Jellyfin，先把管理员建好再去做别的：隧道一开，理论上谁碰到这个地址都进得来，这一步别拖。

本篇教程结束

---

原文：<https://www.passnat.com/posts/jellyfin-nas-media-server-private-cinema>
