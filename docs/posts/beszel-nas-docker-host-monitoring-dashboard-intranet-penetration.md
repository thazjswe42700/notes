---
title: "Beszel 主机监控：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源监控面板 Beszel：一个中心加一个客户端，CPU、内存、磁盘、网络、负载和每个容器的占用画成曲线，时间范围从一分钟到三十天；用帕斯内网穿透拿公网地址，出门用手机也能看家里 NAS 的状态。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/beszel-nas-docker-host-monitoring-dashboard-intranet-penetration
---

## 项目介绍

Beszel 是一个开源的主机监控面板，Go 写的，整个程序很小。它分两部分：一个中心（hub）负责存数据和出页面，一个客户端（agent）装在被监控的机器上负责采集。

采的东西包括 CPU 使用率、内存和 SWAP、根分区用量、磁盘读写、网络带宽、系统负载、运行时间；另外把每个容器的 CPU、内存、网络单独列一张表，方便看是哪个服务在吃资源。磁盘还能看 S.M.A.R.T. 的健康状态。

时间范围可以切成一分钟、一小时、十二小时、一天、一周、三十天。

项目地址：[点我跳转](https://github.com/henrygd/beszel)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Beszel 用 Docker Compose 装起来，这样人不在家也能打开面板。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8090（就是下面编排里面板那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 beszel，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  beszel:
    image: henrygd/beszel:latest
    container_name: beszel
    restart: unless-stopped
    ports:
      - "8090:8090"
    volumes:
      - ./beszel_data:/beszel_data

  beszel-agent:
    image: henrygd/beszel-agent:latest
    container_name: beszel-agent
    restart: unless-stopped
    network_mode: host
    volumes:
      - ./beszel_agent_data:/var/lib/beszel-agent
      - /var/run/docker.sock:/var/run/docker.sock:ro
    environment:
      LISTEN: 45876
      KEY: "ssh-ed25519 换成面板给你的公钥"
      HUB_URL: http://localhost:8090
```

**KEY**：这一行先按原样留着，等面板起来、点「添加系统」的时候会给你一串公钥，复制回来替换掉「换成面板给你的公钥」这几个字，再重启客户端。

**network\_mode: host**：客户端要用主机网络，才能读到宿主机的网络流量；root 下挂的 docker.sock 是用来读容器列表和每个容器的占用的。

**HUB\_URL**：客户端主动连面板用的地址。面板和客户端都在同一台 NAS 上，写 http://localhost:8090 就行。

勾上「创建项目后立即启动」。客户端第一次连不上是正常的——那时公钥还没填，等填好重启就行。

### 三、第一次打开：建管理员账号

用公网地址（或者局域网地址加 8090 端口）打开，第一次会跳到创建管理员页面，填邮箱、密码、确认密码，站点名可以随便写，点「创建账户」。不需要配发信服务器，也不用收验证邮件。

登进去是空的「所有客户端」，右上角就是「添加系统」。

### 四、添加系统：把公钥填回去

点「添加系统」，弹窗里填三样：

**名称**：显示在面板上的名字，比如「家的 NAS」。

**主机/IP**：这台 NAS 的局域网地址。

**端口**：默认 45876，不动它，跟编排里的 LISTEN 一致。

下面的「公钥」和「令牌」是自动生成的。把公钥整串复制出来，替换编排里 KEY 那一行，保存并重启客户端（在 Compose 项目里点「重新部署」或者先停止再启动）。回到面板，这一行就会从灰点变成绿点，写着「在线」。

第一次出数据要等一分钟左右，客户端每分钟上报一次。

### 五、面板怎么看

首页「所有客户端」是按机器列的表：CPU、内存、磁盘三条进度条的颜色会随着占用变，负载三个数字分别是 1 分钟、5 分钟、15 分钟的平均值，右侧是网络速率和运行时间。右上角的「视图」可以切成卡片模式。

点名字进详情页，一屏十张图：CPU 使用率、Docker CPU、内存使用率、Docker 内存、根分区用量、根分区读写、带宽、Docker 网络读写、SWAP、系统负载。右上角的时间范围可以切成一小时、一天、一周、三十天，图上能直接看出哪天几点有个尖峰。

上面的「容器」页把所有容器的名称、CPU、内存、网络、健康检查、端口、镜像、状态和更新时间排成一张表，按 CPU 或内存点一下表头就能排序，谁在吃资源一目了然。

「S.M.A.R.T.」页看硬盘的健康状态和通电时间。

出门在外用手机打开同一个公网地址，版面会自动收成一列，图表照样能看。

### 几点说明

**没有温度数据。** 采集温度需要宿主机暴露传感器，虚拟化环境里往往读不到，所以温度那一列是空的。物理机上如果也没有，多半是容器没拿到 /sys 的权限，这不影响 CPU、内存、磁盘这些主要指标。

**部分容器的内存显示为空。** 个别容器（尤其是用了特殊 cgroup 配置的）读不到内存统计，客户端日志里会写一条 bad memory stats，这一列显示为空，其余指标正常。

**客户端一定要用主机网络。** 换成普通 bridge 网络，网络流量这一项就变成容器自己的，不是整台 NAS 的。

**更新。** 镜像标签用的是 latest，升级之前把 beszel\_data 目录备份一份，再到 Docker 里重新拉取镜像、启动项目。

---

原文：<https://www.passnat.com/posts/beszel-nas-docker-host-monitoring-dashboard-intranet-penetration>
