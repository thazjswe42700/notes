---
title: "自建 changedetection.io：飞牛 NAS Docker 部署 + 内网穿透外网访问"
date: 2026-09-07
description: "网页一变就知道。changedetection.io 在飞牛 NAS 上的 Docker 部署全过程，含编排文件、访问口令设置，以及用 PassNAT 内网穿透开放到公网的完整步骤。"
canonical: https://www.passnat.com/posts/changedetection-io-website-change-monitor-nas-docker-deployment-intranet-penetration
---

## 项目介绍

changedetection.io 是一个开源的网页变更监控工具。你把一个网址交给它，它按你设定的间隔去抓这个页面，内容一变就把前后两次摆在一起，删掉的标红、新加的标绿，改了哪个字都看得见。

常见的用法有两类：一类是盯商品页，等降价或者补货；一类是盯官网、公告页、更新日志，有新内容第一时间知道。它也支持按 CSS 选择器、XPath、JSONPath 只盯页面里的某一块，以及通过邮件、Telegram、Webhook 等方式推送通知。

项目地址：[点我跳转](https://github.com/dgtlmoon/changedetection.io)

## 部署安装

changedetection.io 装在自己的 NAS 上之后，只能在家里的网络访问。想在外面也能打开，家里又没有公网 IP，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网 —— 飞牛的应用中心里就能直接装到插件，不用另外找工具。下面这套流程在飞牛 NAS 上从头到尾走一遍。

### 第一步：准备编排文件

作者本人在 Docker Hub 和 ghcr 上都发了官方镜像，直接拉就行，不用自己构建。下面这份编排可以直接用，三处需要你按自己的情况认一下：端口、数据目录、时区。

```yaml
services:
  changedetection:
    image: dgtlmoon/changedetection.io:latest
    restart: always
    ports:
      - "5025:5000"
    volumes:
      - ./datastore:/datastore
    environment:
      - TZ=Asia/Shanghai
```

端口：冒号左边的 5025 是待会儿在浏览器里输的宿主端口，右边的 5000 是容器内部的端口，不要动。5025 如果和你机器上别的服务撞了，换一个没被占用的即可。

数据目录：./datastore 是相对于项目目录的路径，监控记录、历史快照都存在这里。用相对路径的好处是，在飞牛的「文件管理」里就能直接看到这个文件夹。

时区：不设的话容器按 UTC 走，界面上的时间戳和「多久查一次」的计划时间会和你差八小时。

### 第二步：在飞牛里建 Compose 项目

打开飞牛桌面上的 Docker，切到上面的「Compose」，点右上角的「新增项目」。项目名称填 changedetection。

路径这一栏点开，在「我的文件」下面新建一个空文件夹选中它。挑一个空目录就行。

来源选「创建docker-compose.yml」，把上面那段编排整段贴进去，勾上「创建项目后立即启动」，点确认。镜像拉完项目就会自己跑起来，回到列表能看到它是运行中。

### 第三步：先设一个访问口令

changedetection.io 默认是不带任何鉴权的，谁打开地址谁就能改配置，而且它会替你去抓你填进去的任意网址。所以在把它开放到公网之前，一定要先设一个访问口令。

浏览器里输入 NAS 的内网地址加 5025 打开它，左边侧栏那个地球图标可以把界面切成中文。然后进「Settings」，在 Password 那一栏填一个口令，拉到底点 Save 就生效了。

### 第四步：用 PassNAT 插件开放到公网

在飞牛的应用中心里搜 PassNAT，装上，用账号登一次。打开插件切到「隧道」，点「创建隧道」。

隧道名称随便起（注意不能超过 12 个字符），接入节点和传输协议保持默认。本地 IP 这一栏不用动，留空默认就是 127.0.0.1 —— 插件本来就跑在 NAS 上，回环地址天然指向本机。本地端口填 5025，也就是上面编排里冒号左边那个。

创建完回到隧道列表，点开这条隧道的详情，按标题行最左边那个电源图标启动它，状态转成「运行中」。详情里「公网连接」那一列的地址，就是你在外面要访问的地址。

### 第五步：加一个监控

用刚才那个公网地址打开 changedetection.io，最上面那一栏「Web page URL」里贴进要盯的网址，点右边的「监控项」就加上了。

点进这一条可以设「多久查一次」，默认是三分钟。等它抓到第二次、内容和上一次不一样的时候，列表的「变更」那一列就会亮起来，点进去看到的就是前后两次的对比。

## 一些说明

这套编排跑的是纯抓取模式，抓的是页面直接返回的内容。如果你要盯的页面必须跑完 JavaScript 才出内容，官方编排里还提供了一个可选的浏览器容器，另外接上即可，代价是要多占一块内存。

另外，监控间隔不建议设得太密。同一个页面几秒钟抓一次，对方的站点会当成异常流量，你自己的 NAS 也在做无用功。

---

原文：<https://www.passnat.com/posts/changedetection-io-website-change-monitor-nas-docker-deployment-intranet-penetration>


## 相关文章

- [Navidrome 飞牛 NAS 部署与公网听歌](navidrome-docker-private-music-server.md)
- [Grocy 飞牛 NAS 部署与公网查库存](grocy-self-hosted-nas-deployment-internal-network-penetration-remote-access-food-inventory.md)
- [Homebox 飞牛 NAS 部署与公网查物品档案](home-asset-management-system-nas-deployment.md)
- [Jellyfin 家庭影院：飞牛 NAS Docker 部署 + 内网穿透，出门手机接着看](jellyfin-nas-media-server-private-cinema.md)
- [AdventureLog 自建旅行地图：NAS Docker 部署 + 行程分享](open-source-travel-log-adventurelog-docker-deploy-nas-personal-map.md)
