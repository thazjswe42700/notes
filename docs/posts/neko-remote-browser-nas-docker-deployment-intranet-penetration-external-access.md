---
title: "自建neko远程浏览器：飞牛NAS Docker部署+内网穿透外网访问"
date: 2026-09-04
description: "neko 是开源的自托管远程浏览器，Docker 里跑一个完整 Firefox，通过 WebRTC 推流到任意设备。本文讲飞牛 NAS 上的 Compose 部署，并说明为什么必须先建隧道拿到公网端口再写编排。"
canonical: https://www.passnat.com/posts/neko-remote-browser-nas-docker-deployment-intranet-penetration-external-access
---

## 项目介绍

neko 是一个开源的自托管远程浏览器。它把一个完整的 Firefox 跑在 Docker 容器里，再通过 WebRTC 把画面推到你的浏览器上——你在手机、平板或者公司电脑上打开一个网页，操作的其实是家里 NAS 上那个浏览器。

它支持多个人同时进入同一个会话，看到的是同一块屏幕，鼠标键盘的控制权可以随时移交。适合远程协作、和朋友一起看视频，也适合在一个受控的环境里打开不放心的网页。

项目地址：[点我跳转](https://github.com/m1k1o/neko)

## 部署安装

neko 装在内网之后，出了门就访问不到了——我家是移动宽带，没有公网 IP。想在外网也能用，需要配一个内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，飞牛的应用中心里就能直接装到插件。

### 为什么必须先建隧道

neko 的画面走 WebRTC。它必须提前知道「客户端最终从哪个地址和端口连进来」，并把这个地址端口写进连接候选里告诉浏览器。而这个公网端口是穿透服务分配的，事先并不知道。

**所以顺序必须是：先建隧道拿到公网端口，再写编排文件。**反过来做的话，编排里填的端口和隧道分配的端口对不上，网页能打开、能登录，但画面一直是黑的——这是最容易踩的坑。

### 第一步：建隧道

飞牛 NAS 的应用中心里直接搜 PassNAT 就能装到客户端，装好之后登录一次账号。

打开插件，进「隧道」，点创建隧道。传输协议选 TCP，本地 IP 填 127.0.0.1，端口映射加两条：一条 8080 走网页，一条 59000 走画面。

建好之后按一下电源连接。在隧道详情的端口列表里能看到两条公网连接，形如「节点IP:端口」。把 59000 那一条对应的公网端口记下来，下一步要用。

本文示例拿到的是：网页 38957，画面 32480。你的端口号会跟这里不一样，按自己的填。

### 第二步：写编排文件

回到 Docker，切到 Compose 这一页，点新增项目。项目名称随便写，这里叫 neko。

路径点右边的图标，在弹出来的框里新建一个文件夹。**必须选空目录**——里面要是已经有 docker-compose.yml，飞牛会弹一个提示把下面的选项挡住。另外新建出来的文件夹第一次可能选不中，把选择框关掉重新点开就能选了。

来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
name: neko

services:
  neko:
    container_name: neko
    image: ghcr.io/m1k1o/neko/firefox:latest
    restart: unless-stopped
    shm_size: "2gb"
    ports:
      - '8080:8080'
      - '59000:32480'
    environment:
      NEKO_DESKTOP_SCREEN: 1920x1080@30
      NEKO_MEMBER_MULTIUSER_USER_PASSWORD: 你的观看口令
      NEKO_MEMBER_MULTIUSER_ADMIN_PASSWORD: 你的管理口令
      NEKO_WEBRTC_TCPMUX: 32480
      NEKO_WEBRTC_NAT1TO1: 你的节点IP
```

有三个地方要按你自己的情况改：

一、ports 第二条冒号后面的端口，和 NEKO\_WEBRTC\_TCPMUX，**这两处必须填成同一个值**，就是第一步记下来的那个公网端口（示例是 32480）。少改一处，公网上就只有黑屏。

二、NEKO\_WEBRTC\_NAT1TO1 填你隧道的节点 IP。

三、两个口令自己设。admin 那个能操作浏览器，user 那个只能看不能动。

shm\_size 保持 2G。共享内存给少了浏览器会黑屏。

### 第三步：创建并启动

勾上「创建项目后立即启动」，点确认。等镜像拉完、容器起来，用浏览器打开网页那条公网地址（示例是节点IP:38957），输入 admin 口令就进去了。换一个网络、用手机流量打开也是一样的。

### 改编排不能直接改

项目跑起来之后，编排文件在面板里是只读的。而且**改完之后靠「停止再启动」是不生效的**——容器不会因为停一次就按新配置重建，环境变量还是旧的。必须把当前配置清掉、重新构建，改动才会真正生效。

这也是本文把建隧道放在最前面的原因：一次把端口写对，就不用回头折腾这一步。

---

原文：<https://www.passnat.com/posts/neko-remote-browser-nas-docker-deployment-intranet-penetration-external-access>


## 相关文章

- [自托管联邦即时通讯Databag部署指南，树莓派轻量级私密通讯解决方案教程](self-hosted-databag-federated-messaging-deployment-guide-raspberry-pi-lightweight-private-communication.md)
- [自建私人书签管理库 一处部署随处访问，OneNav Docker极简快速部署指南](onenav-docker-bookmark-manager-deployment-guide.md)
- [浏览器WebRTC视频通话应用Call-Me，NAS一键部署，支持屏幕共享与内网穿透远程通话](browser-webrtc-video-call-app-call-me-nas-one-click-deployment-screen-sharing-intranet-penetration-remote-calling.md)
- [浏览器P2P文件传输工具FastSend，WebRTC加密传输，NAS极简Docker部署，局域网极速秒传无限制](fastend-p2p-file-transfer-webrtc-nas-docker.md)
- [mTab书签管理工具私有部署指南，跨平台同步无广告，内网穿透远程访问](mtab-bookmark-manager-private-deployment-guide-cross-platform-sync-no-ads-intranet-penetration-remote-access.md)
