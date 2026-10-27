---
title: "PicoShare 文件分享：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署轻量文件分享 PicoShare：选一个文件上传，自动生成分享链接，朋友打开就能直接预览或下载，不用注册、没有广告、不转码也不改文件名；设一个上传口令，只有自己能传能删；用帕斯内网穿透拿公网地址，在外面也能传文件、发链接。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/picoshare-nas-docker-file-sharing-upload-link-intranet-penetration
---

## 项目介绍

PicoShare 是一个自托管的轻量文件分享工具，定位很直接：把文件传上去，拿一条链接，发给你想发的人。它不追求做成网盘，没有文件夹树、没有复杂的权限体系，页面就三块——上传、文件列表、访客链接。

它有几个特点：只有你自己能上传，别人拿到链接只能看和下载；访客不用注册账号、不弹广告；文件原样保存，不压缩、不转码，文件名和元数据都不改。所以它适合临时传几张原图、一个安装包、一份文档给朋友或同事，而不是长期归档。

上传时可以设一个有效期（一天到一年，或者永不过期），到期自动删除；也可以给文件加一条只有自己看得到的备注。文件列表里能看到每个文件的大小、上传时间和过期时间。项目地址：[点我跳转](https://github.com/mtlynch/picoshare)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 PicoShare 用 Docker Compose 装起来。先穿透、后部署，装完直接用公网地址打开，以后在外面也能传文件、发链接。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3719（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 picoshare，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  picoshare:
    image: mtlynch/picoshare:latest
    restart: unless-stopped
    ports:
      - "3719:4001"
    environment:
      PORT: 4001
      PS_SHARED_SECRET: change-me-upload-secret
    volumes:
      - picoshare_data:/data

volumes:
  picoshare_data:
```

这段编排里要改的只有上传口令：

**change-me-upload-secret**：就是 PS\_SHARED\_SECRET 这一行。设了它，打开站点要先输入这串口令才能上传和管理文件；访客拿到分享链接下载是不用口令的。换成你自己的、长一点的字符串。

端口 3719 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像拉下来之后几秒就起来了。

文件都存在 picoshare\_data 这个具名卷里，备份这一个卷就够了；它默认把文件存在本地磁盘，不依赖 S3 之类的外部对象存储。

### 三、上传一个文件，拿到链接

用公网地址打开，先输入刚才设的上传口令登录。进到「Upload」页：点虚线框选一个文件，或者直接在下面的框里粘贴一段文本；选一个有效期，想加备注就填一句（备注只有你自己看得到）。

选好文件，页面会显示「Upload complete!」，下面就是两条链接：Full Link 带着文件名，Shortlink 只有一串短码。复制任意一条发给对方就行。

### 四、访客打开、你管理文件

对方收到链接，在浏览器里打开就能直接看到文件——图片直接显示原图，文本直接显示内容，其它类型会下载。整个过程不用注册、没有广告，也不会被转码或改文件名。

回到「Files」文件列表，每个文件一行：文件名、备注、大小、上传时间、过期时间，右边三个按钮分别是下载/查看、编辑、复制链接。

想改有效期或备注，点编辑进去改完保存；不想要了就在编辑页点红色的「Delete」，确认一下就永久删掉，列表里随即消失。文件到期后也会自动删除。

### 几点说明

**数据在哪。** 文件都在 picoshare\_data 卷里，备份这个卷就行；想搬到别的机器，把卷导出来带走。

**升级。** 镜像标签用的是 latest，升级前先把卷备份一份，再到 Docker 里重新拉取镜像、重启项目。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，PicoShare 本身不需要 HTTPS 才能用；但分享链接谁拿到谁都能打开，公网地址和链接别到处发，用完了可以在插件里把隧道关掉。

**不接外部存储。** 这篇用的是本地卷，文件和数据库都在 NAS 上；需要接 S3 兼容对象存储的场景这篇不讲。

---

原文：<https://www.passnat.com/posts/picoshare-nas-docker-file-sharing-upload-link-intranet-penetration>


## 相关文章

- [Gokapi 文件分享：NAS Docker 部署 + 内网穿透](gokapi-nas-docker-file-sharing-expiry-auto-cleanup-intranet-penetration.md)
- [Seafile 私有文件库：NAS Docker 部署 + 内网穿透](seafile-nas-docker-private-file-library-intranet-penetration.md)
- [copyparty 自建文件服务器：飞牛 NAS Docker 部署 + 内网穿透外网访问](copyparty-self-hosted-file-server-nas-docker-deployment-intranet-penetration.md)
- [自托管视频分享平台 Fireshare 部署指南，NAS Docker 一键启动分享游戏录屏](self-hosted-video-sharing-fireshare-deployment-guide-nas-docker-one-click-start-share-game-recordings.md)
- [私密文件分享平台 ProjectSend Docker 部署指南，NAS 内网穿透安全共享文件](private-file-sharing-projectsend-docker-deployment-nas-intranet-penetration-secure-file-sharing.md)
