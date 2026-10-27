---
title: "PassNAT 内网穿透 - 高性能 FRP 内网穿透服务"
date: 2026-09-04
description: "PassNAT 提供基于 FRP 的内网穿透服务，支持 TCP、UDP、HTTP、HTTPS 等协议，适合开发测试、远程访问、NAS、游戏联机和企业内网服务发布。"
canonical: https://www.passnat.com/posts/litepan-multi-cloud-storage-aggregation-nas-docker-deployment-intranet-penetration-external-access
---

## 项目介绍

LitePan 是一个开源的轻量级多网盘聚合工具。它把散在各处的存储——本机目录、WebDAV、以及各家网盘——接进同一个界面，你不用为了找一个文件在好几个客户端之间来回切。

它还能把聚合出来的内容挂成本地目录（FUSE），别的程序直接当普通文件夹用；另外带跨盘秒传、STRM 生成这些实用功能。

这篇按飞牛 NAS 的 Docker Compose 图形界面来部署，最后用 PassNAT 把它开放到公网，出门在外照样能用。

项目地址：[点我跳转](https://github.com/Ponphil/LitePan)

## 一、部署

### 准备一个空目录

先在文件管理里新建一个空文件夹，比如 litepan-app。注意一定要是空的。

### 新增 Compose 项目

打开 Docker 应用，切到 Compose 标签，点「新增项目」。项目名称随便起，路径选刚建的那个空文件夹，来源勾「创建 docker-compose.yml」，然后把下面这段整个贴进去。

```yaml
# LitePan · 飞牛 NAS
# 镜像 tag 用 beta —— latest 还是旧的 Python 版，别用错。
services:
  litepan:
    image: ponphil/litepan:beta
    container_name: litepan
    # 访问端口 5211 —— host 网络下不用写 ports，直接开在 NAS 上
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - ./data:/app/data
      - ./strm:/app/strm
      - ./mounts:/app/mounts:shared
      # 想让 LitePan 看到的目录，挂进来才能在「添加本地存储」里选到。只读更稳。
      - /vol1/1000/litepan-demo:/nas:ro
    # 下面这几行是本地挂载（FUSE）要用的，别删
    devices:
      - /dev/fuse:/dev/fuse
    privileged: true
    pid: "host"
    network_mode: "host"
    restart: always
```

### 编排里要认的三处

访问端口 5211。这份编排用的是 host 网络，所以没有 ports 段，服务直接开在 NAS 上，浏览器用「NAS 的地址 + 5211」就能打开。

时区 TZ 改成 Asia/Shanghai，不然界面上的时间会差八个钟头。

数据目录。data、strm、mounts 三个卷用的是相对路径，跟着 Compose 项目目录走，不用去翻绝对路径。最后那行 /vol1/1000/litepan-demo:/nas:ro 是你想让 LitePan 看到的目录——不挂进来的话，后面「添加本地存储」里根本选不到东西。按只读挂更稳。

下面 devices、privileged、pid 那几行是本地挂载（FUSE）要用的，删掉就挂不上。

### 启动

勾上「创建项目后立即启动」，点创建。等它把镜像拉完，容器就起来了。

### 启动后第一件事：改掉默认口令

用 NAS 的地址加 5211 打开，默认账号和密码都是 admin。进后台之后第一件事就是把口令改掉——后面要把它开放到公网，这一步不能省，默认口令挂在公网上等于门开着。

## 二、用 PassNAT 开放到公网

我家是移动宽带，没有公网 IP，装在 NAS 上的服务出门就连不上。这里用 [帕斯内网穿透](https://passnat.com) 做内网穿透，客户端在飞牛的应用中心里直接就能搜到装上，不用另外下载，也不用手写配置文件。

### 在插件里建隧道

装好之后打开插件，切到「隧道」，点「创建隧道」。名字随便起，传输协议选 TCP，接入节点留默认的自动分配。本地 IP 不用动，它默认就是 127.0.0.1；本地端口填 5211。

建好之后打开详情，点最左边那个电源把它连上，状态转成运行中，详情面板里那条「公网连接」就是外面要用的地址。

## 三、接入存储

下面这些用公网地址做就行，跟在内网操作没有区别。

### 添加本地存储

进后台的「存储管理」，点「添加账号」，驱动选「本机存储」，填一个名字，然后点「浏览」，在容器内的目录树里逐层点进刚才挂进来的那个目录，最后「选择当前目录」。

需要接第二个来源就再加一个账号。加完之后，前台左上角的账号下拉可以在几个来源之间切换。

### 挂成本地目录（FUSE）

后台的「文件共享」里有「本地挂载」，可以把聚合出来的内容挂成一个本地目录，别的程序当普通文件夹读写就行。这一项要靠编排里那几行 devices / privileged 才能用。

## 小结

整套跑下来，LitePan 负责把分散的存储收成一个入口，PassNAT 负责让这个入口在外面也能用。编排文件就在上面，照着贴就能起。

---

原文：<https://www.passnat.com/posts/litepan-multi-cloud-storage-aggregation-nas-docker-deployment-intranet-penetration-external-access>
