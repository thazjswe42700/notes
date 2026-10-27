---
title: "哪吒监控自建服务器探针：飞牛 NAS Docker 部署 + 内网穿透外网访问"
date: 2026-09-07
description: "一个容器把家里几台机器的 CPU、内存、硬盘、流量做成实时曲线。本文在飞牛 NAS 上用 Docker Compose 部署哪吒监控面板，并用帕斯内网穿透开放到公网。"
canonical: https://www.passnat.com/posts/nezha-server-monitoring-nas-docker-deployment-intranet-penetration
---

## 项目介绍

家里跑着 NAS、软路由、还有一台常年开着的台式机，哪台在忙、哪台快满了，平时根本没数。哪吒监控就是干这个的：在每台机器上放一个很小的探针，面板上把 CPU、内存、硬盘、网络流量全画成曲线，一眼扫过去就知道谁在喘。

它跟「网站监控」那类工具不是一回事 —— 那类盯的是某个地址还能不能打开，哪吒盯的是**机器本身的负载**。当然它也能顺手加几条 HTTP 检测，两样都要就用它。

面板本体是单个容器，镜像三十兆出头，装在 NAS 上一点不占地方。

项目地址：[点我跳转](https://github.com/nezhahq/nezha)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装哪吒面板，装完再用 [帕斯内网穿透](https://passnat.com) 把它开放到公网 —— 面板挂在公网上才有意义：探针要从各处上报，你自己也想随时随地点开看一眼。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 nezha，路径选一个空文件夹。

「来源」选「创建 docker-compose.yml」，然后把下面这段编排整个贴进去。

### 二、编排文件

```yaml
services:
  nezha:
    image: ghcr.io/nezhahq/nezha
    container_name: nezha
    ports:
      - 8008:8008
    volumes:
      - ./data:/dashboard/data
    restart: unless-stopped
```

### 两处需要认一下

**端口 8008**：冒号左边是 NAS 上对外的端口，被占了就改成别的。注意这一个端口是**两用**的 —— 网页面板走它，各台机器上的探针上报数据也走它，所以后面建隧道只要开这一个就够。

**./data**：数据目录，用的是相对路径，就落在刚才选的那个项目目录下面。机器列表、历史曲线、设置都存在这儿，备份这个文件夹就等于备份了整个面板。

填完勾上「创建项目后立即启动」再点创建。镜像三十兆出头，拉完很快就跑起来了。

### 三、用 PassNAT 开放到公网

容器起来之后先别急着在家里打开。在飞牛的应用中心装上 PassNAT 插件，打开它，直接在插件里新建一条隧道：名字随便起，传输协议选 TCP，本地地址留空（默认就是 127.0.0.1），本地端口填 8008。

建好按一下电源启动，隧道详情里就会给出一个公网地址。**接下来的所有步骤都在这个公网地址上做** —— 改口令、加机器、看曲线，人在外面也是这一套。

### 四、先把出厂口令改掉

用公网地址打开看到的是公开看板，管理端在地址后面加一个 /dashboard。这个面板**出厂就带一个默认账号**，不像别的应用那样第一次打开让你建管理员 —— 所以进去之后第一件事就是把口令改成自己的：右上角进「个人资料」，把用户名和口令都改掉，**设一个强口令** —— 管理端能改所有机器的配置，而它是挂在公网上的。

### 五、把机器加进来

先去后台的「设置」里把两样填上：**站点名字**随便起一个，**Agent 连接地址**填上一步拿到的公网地址（形如 IP:端口），然后点页面最下面的 Confirm 保存。这两项是一起的 —— 站点名字空着的话，Confirm 点了没有任何反应，只在输入框下面留一行 Invalid input。

回到「Server」页，右上角的「Installation command」点开，选你那台机器的系统，安装命令就复制到剪贴板了。在那台机器上跑一下就完事 —— Windows、Linux、macOS 官方都有对应的探针，探针自己会注册到面板上，不用先在网页里建条目。上一步的连接地址要是没填，这里会直接弹一个 Error。

探针跑起来之后回看板刷新一下，那台机器就上来了，CPU、内存、流量的曲线开始走。想再加机器就重复这一步，面板上会多一张卡。

顺带一提，后台的「服务」那一栏可以加 HTTP 或者 TCP 检测，填个地址它就定时去探，挂了会记一笔 —— 机器和网站两样都盯的话，一个面板就够。

---

原文：<https://www.passnat.com/posts/nezha-server-monitoring-nas-docker-deployment-intranet-penetration>


## 相关文章

- [Beszel 主机监控：NAS Docker 部署 + 内网穿透](beszel-nas-docker-host-monitoring-dashboard-intranet-penetration.md)
- [自建 cAdvisor 容器监控：飞牛 NAS Docker 部署 + 内网穿透](cadvisor-docker-container-resource-monitoring-nas-deployment-intranet-penetration.md)
- [IOPaint开源AI图像修复工具，免费自托管，CPU/GPU/M1支持，智能去物体移除水印](iopaint-open-source-ai-image-inpainting-tool-free-self-hosted-cpu-gpu-m1-support-remove-objects-watermarks.md)
- [Homepage 自建 NAS 导航首页：飞牛 Docker 部署 + 内网穿透外网访问](homepage-nas-service-dashboard-docker-deployment-intranet-penetration.md)
- [Homarr 自建服务导航页：飞牛 NAS Docker 部署 + 内网穿透外网访问](homarr-self-hosted-dashboard-nas-docker-deployment-intranet-penetration.md)
