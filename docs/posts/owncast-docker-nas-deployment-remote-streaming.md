---
title: "自建独立直播平台Owncast，Docker极简部署NAS，内网穿透远程直播间"
date: 2025-10-16
description: "Owncast是开源自托管直播平台，让你完全掌控直播内容、观众和数据。支持RTMP协议，兼容OBS等直播软件。本文详细介绍Owncast在NAS上的Docker部署方法，包括端口映射、存储配置和RTMP推流设置，帮助你快速搭建独立自由的个人直播间。"
canonical: https://www.passnat.com/posts/owncast-docker-nas-deployment-remote-streaming
---

## 项目介绍

Owncast 是一个开源、自托管的单用户直播服务器：直播画面、聊天室、观众数据都跑在你自己的设备上，不经过任何第三方平台。它支持标准的 RTMP 协议，OBS 这类常见推流软件直接就能用，观众那边不需要装任何东西，浏览器打开链接就能看直播、发弹幕。

它很适合在家里搭一个自己的直播间：游戏直播、桌面分享、家里的摄像头画面，推流从家里出，直播间开在 NAS 上，在哪播、谁在看都自己定。

项目地址：[点我跳转](https://github.com/owncast/owncast)

## 部署安装

如果你的朋友在外面、家里宽带又没有公网 IP，就需要内网穿透把直播间开放出去。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的操作方法可以 [点我查看内网穿透全平台上手攻略](https://passnat.com)，轻松实现随时随地访问自己的直播平台。

### 一、新建 Compose 项目

下面在飞牛 NAS 上用 Docker Compose 部署 Owncast。打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 **owncast**，路径在「我的文件」下面挑一个空文件夹（演示里是 live-demo/owncast-app），没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去。

```yaml
services:
  owncast:
    image: owncast/owncast:latest
    network_mode: bridge
    ports:
      - "3491:8080"
      - "1935:1935"
    volumes:
      - ./data:/data
    restart: unless-stopped
```

从上往下认两处：**3491** 是看直播的端口（映射到容器里的 8080），**1935** 是接收推流的端口；直播数据就存在 **./data** 文件夹里，跟着你刚选的路径走。

勾上「创建后立即启动」，点确认。第一次会下载镜像，等一会儿，列表里这个项目变成「正在运行」就可以了。

### 二、建隧道，拿到公网地址

在飞牛自带的应用中心里搜「PassNAT」，装上这个内网穿透插件，用 PassNAT 账号登录后打开它。点「创建隧道」，名字填 owncast，本地端口填 **3491**，本地地址保持默认的 127.0.0.1 不动，点创建。

创建好之后按这一行的电源键，等状态变成「运行中」。打开详情页，这里有一个公网地址——这就是朋友在外面看直播的入口。

### 三、改掉默认管理口令

浏览器打开这个公网地址，在网址后面加上 /admin 进管理后台。浏览器会先弹一次登录框：默认用户名 admin，默认口令 abc123。

第一次进来先把口令改掉：在「Server Setup」里找到 Admin Password，换成自己的密码（要有大写字母、小写字母、数字和特殊符号，不能带横线），点 Update 保存。同一个页面的「Stream Keys」标签里是推流钥匙，推流软件靠它认证，也顺手换掉。

### 四、改直播间信息

在「Configuration」的「General」里，把服务器的名字（Name）和简介（About）改成自己的，这些就是观众进来看到的东西。改完点「Save Message」保存。

### 五、开始直播

推流软件（OBS 或其他支持 RTMP 的工具）里，服务器地址填 **rtmp://你NAS的局域网地址:1935/live**，流钥匙填上一步换好的那把。开始推流，管理后台右上角的状态会从 OFFLINE 跳成 LIVE，直播间就开了。

朋友在外面打开第二步拿到的公网地址，点一下播放就能看；页面上还能发弹幕，你在直播间里马上就能看到。

## 总结

Owncast 把直播这件事收回到自己家里：画面从家里推，直播间开在 NAS 上，弹幕和观众都在自己的页面上。配上帕斯内网穿透，没有公网 IP 也能让朋友随时点开就看。照着上面六步做，几分钟就能开播。

---

原文：<https://www.passnat.com/posts/owncast-docker-nas-deployment-remote-streaming>


## 相关文章

- [自建IPTV直播平台无需公网，Docker快速部署秒播体验，内网穿透随处访问管理](self-hosted-iptv-live-streaming-platform-docker-deployment-no-public-ip-fast-playback-internal-network-penetration.md)
- [高效文本分享平台Hasty Paste II，Docker快速部署NAS私有服务，支持内网穿透远程访问](hasty-paste-ii-docker-nas-private-service-intranet-penetration.md)
- [自建网络电台完整方案 AzuraCast Docker 快速部署指南 NAS 轻松搭建](azuracast-docker-nas-wangluo-diantai-kuaisushubu.md)
- [StackEdit开源Markdown编辑器完整部署指南，Docker一键启动支持内网穿透远程访问](stackedit-docker-deployment-guide-internal-penetration-remote-access.md)
- [远程同步观影神器SyncTV，NAS一键部署，与全球好友实时同步看电影直播](sync-tv-remote-synchronized-movie-watching-nas-one-click-deployment-real-time-global-friends.md)
