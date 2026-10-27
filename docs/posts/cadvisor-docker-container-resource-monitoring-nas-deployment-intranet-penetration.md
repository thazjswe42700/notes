---
title: "自建 cAdvisor 容器监控：飞牛 NAS Docker 部署 + 内网穿透"
date: 2026-09-08
description: "NAS 上跑了一堆 Docker 容器，哪个在吃 CPU 和内存？在飞牛 NAS 上用 Docker Compose 自建 cAdvisor，每个容器单独一页、一秒一个采样点画成曲线，再用内网穿透随时随地看一眼。含完整编排文件和逐行说明。"
canonical: https://www.passnat.com/posts/cadvisor-docker-container-resource-monitoring-nas-deployment-intranet-penetration
---

## 项目介绍

cAdvisor 全名 Container Advisor，是 Google 开源的容器资源监控。装一个容器，它就把这台机器上每一个 Docker 容器各自吃了多少 CPU、多少内存、网络跑了多少、文件系统占了多少，全都采下来画成曲线。

它跟别的监控最不一样的地方是：它是按容器分的。一个容器单独一页，进去就是这一个容器的 CPU、内存、网络、文件系统四组曲线，而且是一秒一个采样点，往回还能看一小段。Kubernetes 里 kubelet 内置的容器指标采集器就是它。

顺便说清楚它和另外几类的分工：Uptime Kuma 那种盯的是「我的网站还活着吗」，探针类盯的是「我这十几台机器都还好吗」，整机监控盯的是「这一台机器现在到底在忙什么」，而 cAdvisor 盯的是「这台机器上的每一个容器，各自吃了多少」。

项目地址：[点我跳转](https://github.com/google/cadvisor)

## 部署安装

监控这东西的价值就在于随时能看一眼。装在内网，出了门就看不到，那它一半的用处就没了。想省事可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里就能装到客户端，隧道直接在插件里建。

我家是移动宽带，没有公网 IP，拿公网 IP 也没那么简单。为了快速方便，直接用内网穿透是最省事的一条路。

### 第一步：准备编排文件

下面这份可以直接用：

```yaml
services:
  cadvisor:
    image: ghcr.io/google/cadvisor:latest
    container_name: cadvisor
    ports:
      # 左边 3380 是 NAS 上开的口，右边 8080 是它自己的
      - "3380:8080"
    volumes:
      # 下面这些都是只读挂进去给它采数据用的
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
      - /dev/disk/:/dev/disk:ro
    privileged: true
    devices:
      - /dev/kmsg
    restart: unless-stopped
```

这份编排有三处值得单独说：

① ports 那一行 3380:8080。左边 3380 是 NAS 上开出来的口，右边 8080 是 cAdvisor 自己的默认端口。左边这个数字你可以随便换成没被占用的，但后面建隧道要填的是它，别填成 8080。

② 中间那一批卷全都带 :ro，是只读挂进去给它读数据的：/ 是宿主机根目录，/var/run 里有 Docker 的套接字，/sys 是内核暴露的 cgroup 指标，/var/lib/docker 是容器的存储层，/dev/disk 用来认磁盘。它就靠这些采数据。

③ 最后 privileged: true 和 devices: /dev/kmsg 这两行，是官方 README 的 docker run 里本来就有的，给了它才看得到整机和所有容器的资源。

镜像这行说明一下：官方 README 给的是一个 VERSION 变量加一句「用最新的 release」，并没有给死一个版本号。所以这里写的是 latest，你什么时候拉，拿到的就是当时的最新版，不用回头来改这份编排。

### 第二步：在飞牛里建 Compose 项目

打开飞牛桌面上的 Docker，切到上面的「Compose」标签，点「新增项目」。

项目名称填 cadvisor。路径这里点右边的图标挑一个空文件夹，没有就在选择器里当场新建一个。

「来源」选「创建 docker-compose.yml」，把上面那段贴进去，勾上「创建项目后立即启动」，点确认。

起来之后浏览器敲 NAS 的地址加 3380，页面就出来了 —— 不用注册、不用配置，打开就是数据。

### 第三步：建隧道开放到公网

飞牛的应用中心里直接搜 PassNAT 就能装到客户端，装完账号登一次就行。

打开插件，进「隧道」，点创建隧道。名字自己起一个，接入节点和传输协议都留默认。

「本地IP」这一栏不用填 —— 留空默认就是 127.0.0.1，插件本来就跑在 NAS 上，回环地址天然指向本机。本地端口填 3380，也就是上面编排里冒号左边那个数。

建好之后在隧道列表里点开详情，按一下最左边的电源图标连接，端口列表里就能看到分配给你的公网地址，浏览器直接敲那个地址就打开了。

### 第四步：只看某一个容器

首页看到的是整台机器的总览。要单看某一个容器，在地址后面接上 /docker/ 再加容器名就行，比如 /docker/cadvisor 就是 cAdvisor 自己这一个容器的页面。

进去之后从上往下依次是 Isolation（这个容器被分到多少核、内存上限多少）、Overview 三个仪表盘、然后就是 CPU、Memory、Network、Filesystem 四组曲线。想比对哪个容器吃得多，换个容器名再敲一次就行。

### 第五步：用完把隧道关掉

cAdvisor 的界面没有登录口令这回事，拿到地址就能看。所以最省事的用法是：要看的时候在插件里把隧道打开，看完顺手关掉，就点一下电源键那一下。

隧道关掉之后公网地址就进不来了，但隧道本身还留着，下次再按一下电源键就回来，地址和端口都不会变。

要是你确实需要它一直开着，可以在前面自己加一层带口令的反向代理，或者只在需要的时候开。

## 常见问题

页面打不开：先确认容器起来了，再确认 3380 这个端口没被别的服务占掉。飞牛的 Docker 里看容器状态最快。

看不到别的容器、只有一串 ID：多半是 /var/run 没挂进去，cAdvisor 拿不到 Docker 的套接字，就只能按 cgroup 路径显示。

Processes 那一栏是空的：这份编排没有加 pid: host，容器看不到宿主机的进程表，所以那一栏就是空的。只看资源曲线的话不影响。

---

原文：<https://www.passnat.com/posts/cadvisor-docker-container-resource-monitoring-nas-deployment-intranet-penetration>
