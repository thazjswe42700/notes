---
title: "自建漫画电子书媒体库 Komga 服务器，内网穿透远程访问，NAS 极简部署指南"
date: 2025-10-16
description: "在飞牛 NAS 上用 Docker Compose 部署 Komga 漫画库，用帕斯内网穿透开放到公网，手机平板接着看，进度自动同步。"
canonical: https://www.passnat.com/posts/komga-nas-media-server-intranet-penetration-remote-access-guide
---

## 项目介绍

Komga 是开源的漫画、漫画书、杂志和电子书媒体库：文件按文件夹放好，它自动整理成带封面的书架，手机、平板、电脑打开就能翻。

它带内置网页阅读器、多用户和按库的权限管理、元数据编辑、重复文件检测；进度记在服务端，换台设备打开还是看到的那一页。有 Kobo 或 KOReader 的话还能跟阅读器同步。

项目地址：[点我跳转](https://github.com/gotson/komga)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装 Komga，再用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，在外面也能接着看。开始前先把漫画文件准备好：按系列放文件夹，打成 cbz（就是 zip 改个后缀），cbr、pdf、epub 也支持。

### 用 Docker Compose 部署

打开飞牛的 Docker，进 Compose，点新增项目，来源选「创建 docker-compose.yml」，把下面的编排贴进去：

```yaml
services:
  komga:
    image: gotson/komga
    container_name: komga
    restart: unless-stopped
    user: "1000:1000"
    network_mode: bridge
    ports:
      - "3494:25600"
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - ./config:/config
      - ../komga-books:/data
```

几处要认的：镜像用官方的 gotson/komga；用户写 1000:1000，跟漫画文件夹的读写权限对上（你 NAS 上文件属主不同就改成自己的）；端口映射写 3494，换成本地没被占用的也行，建隧道时填的是这个数；两个挂载——config 放配置和数据库，komga-books 换成你自己的漫画文件夹。

勾上「创建后立即启动」，点确认，等列表里它变成正在运行。

## 用 PassNAT 开放到公网

飞牛应用中心搜 PassNAT，把内网穿透插件装上。插件里点创建隧道，名字随便起，端口填编排里映射的 3494，本地地址留默认。建好之后按一下电源，绿点亮了，这一行就有公网地址。

打开这个公网地址，先把界面切成简体中文。实例第一次打开会提示还没激活，填好邮箱密码点创建管理员——隧道开着的时候这步要马上做。

## 添加漫画库

点添加库，名字随便写；根目录选 /data，就是编排里挂的那个漫画文件夹；后面一路默认，点添加。它扫一遍，漫画就按系列排好，封面自动配上。

## 开始看

点开一本就能整页翻，看到哪页它都记着，换台设备打开还是这页。手机浏览器打开同一个地址，翻页一样顺手。

---

原文：<https://www.passnat.com/posts/komga-nas-media-server-intranet-penetration-remote-access-guide>
