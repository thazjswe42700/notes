---
title: "自建 Netdata 实时监控：飞牛 NAS Docker 部署 + 内网穿透"
date: 2026-09-07
description: "NAS 现在忙不忙？在飞牛 NAS 上用 Docker Compose 自建 Netdata，装完不用配任何东西就有秒级实时曲线，再用内网穿透随时随地看一眼。含完整编排文件和三处关键说明。"
canonical: https://www.passnat.com/posts/netdata-realtime-server-monitoring-nas-docker-deployment-intranet-penetration
---

## 项目介绍

Netdata 是一个开源的实时监控，装一个容器，整台机器的指标就全采上了：CPU、内存、磁盘读写、网卡流量，还有每一个 Docker 容器各占多少。

它跟别的监控最不一样的两点：一是秒级，曲线一秒一个点，不是几分钟才动一下；二是零配置，装完不用写任何采集规则，它自己就把磁盘、网卡、容器都认出来了。

顺便说清楚它和另外两类的分工：Uptime Kuma 那种盯的是「我的网站还活着吗」，探针类盯的是「我这十几台机器都还好吗」，而 Netdata 盯的是「这一台机器现在到底在忙什么」。

项目地址：[点我跳转](https://github.com/netdata/netdata)

## 部署安装

监控这东西的价值就在于随时能看一眼。装在内网，出了门就看不到，那它一半的用处就没了。

我家是移动宽带，没有公网 IP，拿公网 IP 也没那么简单。想省事可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，不需要公网 IP，也不用改路由器。

### 第一步：准备编排文件

下面这份可以直接用：

```yaml
services:
  netdata:
    image: netdata/netdata:stable
    container_name: netdata
    # 用宿主机的网络和进程空间，它才看得到真实的网卡和进程
    network_mode: host
    pid: host
    cap_add:
      - SYS_PTRACE
      - SYS_ADMIN
    security_opt:
      - apparmor:unconfined
    volumes:
      # 配置和历史数据存这儿，删容器不丢
      - netdataconfig:/etc/netdata
      - netdatalib:/var/lib/netdata
      - netdatacache:/var/cache/netdata
      # 下面这些是只读挂进去给它采数据用的
      - /:/host/root:ro,rslave
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /etc/os-release:/host/etc/os-release:ro
      - /var/log:/host/var/log:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
    restart: unless-stopped
volumes:
  netdataconfig:
  netdatalib:
  netdatacache:
```

这份编排跟大多数教程里的不太一样，有三处值得单独说：

① 它用的是 network\_mode: host，也就是直接用宿主机的网络。所以这份编排里没有 ports 那一行 —— 不需要「左边宿主右边容器」那种端口映射，它直接就占用宿主机的 19999。要是这个端口在你机器上被占了，得去 netdata.conf 里改，不是改编排。

② 中间那三个卷（netdataconfig / netdatalib / netdatacache）是它自己的配置和历史数据，放在命名卷里，删掉容器重建也不会丢。

③ 下面那一批是只读挂进去的宿主机目录，它靠这些采数据：/proc 和 /sys 是系统指标，/var/run/docker.sock 用来认出每个容器叫什么名字，/var/log 用来读日志。都带了 :ro，它只读不写。

pid: host 和那两个 cap\_add 也是采集需要的：没有它们，容器内的进程和网络看不全。

### 第二步：在飞牛里建 Compose 项目

打开飞牛桌面上的 Docker，切到上面的「Compose」标签，点「新增项目」。

项目名称填 netdata。路径这里点右边的图标挑一个空文件夹，没有就在选择器里当场新建一个。

「来源」选「创建 docker-compose.yml」，把上面那段编排贴进编辑框，最后勾上「创建项目后立即启动」，点确认。镜像两百多 M，按网速等一会儿。

起来之后浏览器敲 NAS 的地址加 19999，面板就出来了 —— 这一步不用配任何东西。

### 第三步：建隧道开放到公网

飞牛的应用中心里直接搜 PassNAT 就能装到客户端，装好之后账号登录一次即可。

打开插件，进「隧道」，点创建隧道。名字自己起一个，接入节点和传输协议都留默认。

「本地IP」这一栏不用填 —— 留空默认就是 127.0.0.1，插件本身跑在 NAS 上，回环地址指向的就是这台机器。本地端口填 19999。

建好之后在隧道列表里点开详情，按一下电源图标连接，端口列表里就能看到公网地址。

### 第四步：用完把隧道关掉

这一步是这篇里最该记住的：Netdata 的面板没有登录口令，打开就是全部指标。

所以别让它长期挂在公网上。好在隧道那个电源键本来就是一键开关：想看的时候点开，看完顺手点一下关掉，公网地址立刻就不通了，内网访问不受影响。

要是你确实需要它一直开着，可以在前面加一层带口令的反向代理，或者用 Netdata Cloud 的方式把节点接过去，由它那边做登录 —— 这两条都超出本篇范围了。

## 常见问题

面板打不开：先确认容器起来了，再确认 19999 没被别的服务占用。因为用的是宿主机网络，端口冲突不会像端口映射那样直接报错，而是 Netdata 自己起不来。

容器里看不到别的容器名字，只有一串 ID：多半是 /var/run/docker.sock 没挂上，或者路径写错了。

曲线是断的：宿主机负载太高或者磁盘太慢的时候会丢点，把历史数据卷放在快一点的盘上会好一些。

---

原文：<https://www.passnat.com/posts/netdata-realtime-server-monitoring-nas-docker-deployment-intranet-penetration>
