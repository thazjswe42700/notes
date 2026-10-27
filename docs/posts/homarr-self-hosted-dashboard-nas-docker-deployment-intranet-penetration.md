---
title: "Homarr 自建服务导航页：飞牛 NAS Docker 部署 + 内网穿透外网访问"
date: 2026-09-07
description: "把 NAS 上散落的自建服务收进一个导航首页。本文在飞牛 NAS 上用 Docker Compose 部署 Homarr，并用帕斯内网穿透开放到公网，人在外面也能打开。"
canonical: https://www.passnat.com/posts/homarr-self-hosted-dashboard-nas-docker-deployment-intranet-penetration
---

## 项目介绍

NAS 用久了，上面的服务会越装越多——相册、影音、下载、笔记、各种小工具。每个都是一个端口，时间一长自己都记不住谁是谁，只能靠浏览器书签硬记。

Homarr 是一个开源的自建服务导航页。它把这些服务收进同一个首页，每个服务一张卡片，点一下就进去；卡片上还会显示这个服务当前是在线还是挂了。除了应用图标，它也能摆系统资源、时钟、搜索框这类小组件，把 NAS 的首页变成一个真正能用的入口。

项目地址：[点我跳转](https://github.com/homarr-labs/homarr)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装 Homarr，装完再用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，这样人在外面也能打开自己 NAS 的导航页。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点新建项目。项目名称填 homarr，路径这里选一个空文件夹，没有就在选择器里当场新建一个。

「来源」选「创建 docker-compose.yml」，然后把下面这段编排整个贴进去。

### 二、编排文件

```yaml
services:
  homarr:
    image: ghcr.io/homarr-labs/homarr:latest
    container_name: homarr
    ports:
      - 7575:7575
    environment:
      - SECRET_ENCRYPTION_KEY=换成你自己的64个十六进制字符
    volumes:
      - ./appdata:/appdata
    restart: unless-stopped
```

### 三处需要认一下

**端口 7575**：冒号左边是 NAS 上对外的端口，被占了就改成别的，后面建隧道要填的也是这个。

**SECRET\_ENCRYPTION\_KEY**：必填。Homarr 用它加密存在本机数据库里的其它密钥（你以后接 qBittorrent、Home Assistant 这些服务时，它们的口令和 API key 都存在这儿）。不填的话容器直接退出。

### 这串密钥去哪儿弄

先说清楚格式，因为「64 位」这个说法在中文里容易听错：要的是 **64 个十六进制字符**，也就是 32 字节、256 位——正好是 AES-256 的密钥长度。不是 64 bit。少一位多一位都解析不出来，容器起不来。

生成办法，挑一个就行：

① 打开 Homarr 的官方安装文档页（[homarr.dev 安装文档](https://homarr.dev/docs/getting-started/installation/docker/)），那一页上 SECRET\_ENCRYPTION\_KEY 的值就是**你自己浏览器当场随机生成的**，直接复制过来用。

② 懒得跳转的话：这一栏先随便填点什么，点创建。Homarr 起不来会退出，**它的日志里会打印一个现成的随机密钥**，抄回来填进去再启动一次就行。

③ 电脑上有终端的话，一条命令：**openssl rand -hex 32**。飞牛的网页界面里没有终端，所以这条得在你自己电脑上跑。

**⚠ 别用别人文章里现成的那一串。**网上不少教程直接贴了一个能用的密钥，照抄的话所有人共用同一把——那就等于没加密。这串东西自己生成、自己留着。

**./appdata**：Homarr 的配置、面板布局、图标都存在这儿。用的是相对路径，就落在刚才选的那个项目目录下面，不用去查飞牛把「我的文件」映射到了哪里。

### 三、启动并创建管理员

勾上「创建项目后立即启动」，点创建。镜像一百多兆，拉完很快就跑起来了。

然后在浏览器里打开 http://你的NAS地址:7575，第一次进会让你创建一个管理员账号，建完就直接进面板了。

**这一步建议在内网就做掉**。管理员是「谁先建谁就是」，所以先把它建好，再往下做公网映射比较稳妥。

### 四、往面板上摆东西

进面板之后是一块空白的板子。右上角进编辑模式，就能往上加应用卡片和小组件了。

加应用时填一个名字和这个服务的地址，图标 Homarr 会自己去匹配，常见的自建服务基本都认得。小组件里可以摆一个系统资源，NAS 的 CPU、内存占用在首页就看得见。

### 五、用 PassNAT 开放到公网

面板做好了，但它只在家里的局域网里能打开。我家是移动宽带，没有公网 IP，想在外面访问就得靠内网穿透。

在飞牛的「应用中心」里搜 PassNAT 就能装上客户端，装好账号登录一次。然后直接在插件里建隧道，不用去网页控制台绕一圈：

隧道名称随便起一个，传输协议选 TCP，本地地址那一栏留空就行（默认就是 127.0.0.1，插件跟 Homarr 跑在同一台机器上），本地端口填 7575。接入节点保持自动分配。

创建完在隧道详情里按一下电源按钮启动，详情里会给出一个公网地址，用那个地址就能在外网打开你的 Homarr 面板了。

## 小结

Homarr 本身很轻，一个容器、一个端口、一个数据目录就跑起来了，适合放在 NAS 上当首页用。配合内网穿透之后，人在外面也能从同一个入口看到家里都跑着些什么。

---

原文：<https://www.passnat.com/posts/homarr-self-hosted-dashboard-nas-docker-deployment-intranet-penetration>


## 相关文章

- [Homepage 自建 NAS 导航首页：飞牛 Docker 部署 + 内网穿透外网访问](homepage-nas-service-dashboard-docker-deployment-intranet-penetration.md)
- [自建 Smart Mermaid：飞牛 NAS Docker 部署 + 内网穿透外网访问](smart-mermaid-ai-text-to-diagram-nas-docker-deployment-intranet-penetration.md)
- [copyparty 自建文件服务器：飞牛 NAS Docker 部署 + 内网穿透外网访问](copyparty-self-hosted-file-server-nas-docker-deployment-intranet-penetration.md)
- [Moments 自建朋友圈：飞牛 NAS Docker 部署 + 内网穿透外网访问](moments-self-hosted-private-timeline-nas-docker-deployment-intranet-penetration.md)
- [轻量级个人导航页面 Flare 快速部署教程，飞牛 NAS Docker 部署 + 内网穿透远程访问](lightweight-personal-navigation-flare-quick-deploy-tutorial-nas-one-click-install-guide.md)
