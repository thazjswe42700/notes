---
title: "qBittorrent 飞牛 NAS 部署与远程下载"
date: 2026-10-04
description: "BT 下载任务丢给家里 NAS，到家文件已就位。包含官方镜像单容器编排、容器日志临时密码、PassNAT 插件隧道与远程添加种子步骤。"
canonical: https://www.passnat.com/posts/qbittorrent-nas-remote-bt-downloader-deploy-intranet-penetration
---

## 项目介绍

qBittorrent 是一个开源的 BT 下载器，官方提供 Docker 镜像。把它部署在自己的 NAS 上，下载任务二十四小时挂着跑：在公司把任务丢进去，到家文件已经躺在 NAS 的下载目录里。

本期在飞牛 NAS 上使用官方镜像单容器部署，实测版本为 v5.2.4，镜像约 110 MB。项目源码：[qBittorrent GitHub 仓库](https://github.com/qbittorrent/qBittorrent)，官方镜像说明：[qbittorrentofficial/qbittorrent-nox](https://hub.docker.com/r/qbittorrentofficial/qbittorrent-nox)。

## 部署安装

家里宽带没有公网 IP 的话，装完先别急着用，跟着下一节配一次内网穿透，出门才摸得到它。穿透用的工具是[帕斯内网穿透](https://www.passnat.com)，飞牛应用中心里就有它的插件，注册好账号装上即可。

打开飞牛桌面的 Docker，切到 Compose，新增项目，路径挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这份编排整段粘进去：

```yaml
services:
  qbittorrent:
    image: qbittorrentofficial/qbittorrent-nox:latest
    container_name: qbittorrent
    restart: unless-stopped
    network_mode: bridge
    ports:
      - "8080:8080"         # 网页界面，等下建隧道填的本地端口就是 8080
      - "6881:6881/tcp"     # BT 连接端口，两端都要映射
      - "6881:6881/udp"
    environment:
      QBT_LEGAL_NOTICE: confirm   # 官方镜像要求确认一次使用声明，不填起不来
      QBT_WEBUI_PORT: 8080
      QBT_TORRENTING_PORT: 6881
      PUID: "1000"
      PGID: "1000"
      TZ: Asia/Shanghai
    volumes:
      - ./config:/config        # 配置和账号密码都存这里
      - ./downloads:/downloads  # 下载的文件落在这里
    mem_limit: 1g
    stop_grace_period: 30m      # 停止时给 qBittorrent 时间保存任务进度
```

三处要留意：端口外面里面都写 8080（这个镜像的网页界面对访问域名有校验，宿主端口换成别的会连登录页都打不开）；6881 是 BT 连接口，TCP 和 UDP 两行都要有；QBT\_LEGAL\_NOTICE 必须照抄 confirm，这是官方镜像的要求，少了它容器起不来。两个卷一个放配置，一个放下载的文件。

勾上「创建项目后立即启动」，点确认，镜像一百多兆，拉完容器自己就起来了。

## 首次登录：临时密码在哪

qBittorrent 4.6.1 之后第一次启动不设默认密码，而是生成一个临时密码打印在容器日志里。飞牛上打开 Docker → 容器，找到 qbittorrent，点右侧菜单里的「运行日志」，能看到这样一行：

```
The WebUI administrator username is: admin
The WebUI administrator password was not set. A temporary password is provided for this session: XXXXXXXXXX
You should set your own password in program preferences.
```

用户名 admin，密码就是这一行里的临时密码。登进去第一件事：点开设置（齿轮）→ WebUI 页签 → 认证这一栏点「更改当前密码」，换成自己的密码再保存。

## PassNAT 内网穿透：拿到公网地址

打开飞牛桌面的 PassNAT 插件，直接在插件里创建隧道：类型选 HTTP，子域名自己起一个，本地地址留默认（127.0.0.1），本地端口填 8080，点创建隧道，再按一次电源启动。一个固定的公网网址就到手了，浏览器打开它，就是家里这台 qBittorrent。

## 远程下载：出门丢任务

点工具栏的加号，选「链接」，贴一个 .torrent 或磁力地址就能下。演示用的是 Debian 官方安装镜像的种子，点添加之后进度条当场就跑起来了。下完的文件在编排里 downloads 对应的那个目录里，出门丢的任务，到家已经就位。

## 常见问题

打不开网页界面：先看容器日志里有没有起来，再确认访问端口是 8080——这个镜像对访问域名有校验，带端口的 Host 只认 8080。下载没速度：6881 的 TCP 和 UDP 两行都要映射上，DHT 需要几分钟连网。临时密码登不上：它是一次性的，改过密码就作废；容器重启会生成新的，回日志里再看一眼。

---

原文：<https://www.passnat.com/posts/qbittorrent-nas-remote-bt-downloader-deploy-intranet-penetration>
