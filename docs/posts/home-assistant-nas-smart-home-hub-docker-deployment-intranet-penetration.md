---
title: "Home Assistant 自建智能家居中枢：飞牛 NAS Docker 部署官方镜像，不同牌子的设备一页全管，内网穿透出门手机也能控"
date: 2026-09-07
description: "Home Assistant 是开源的智能家居中枢，米家、飞利浦、HomeKit 这些不同牌子的设备都能接进来，一个页面统一控制，还能设自动化，数据全在自己家。本文用 Docker Compose 在飞牛 NAS 上部署官方镜像（宿主网络模式），再用内网穿透开放到公网，人在外面手机上一样能点灯、看状态。"
canonical: https://www.passnat.com/posts/home-assistant-nas-smart-home-hub-docker-deployment-intranet-penetration
---

## 项目介绍

Home Assistant 是一个开源的智能家居中枢，装在自己的 NAS 上。米家、飞利浦、HomeKit、Matter 这些不同牌子的设备都能接进来，在同一个页面上开灯、看温度、控插座，还能设自动化，比如天黑自动开灯、离家自动关空调。数据全在自己家，不走别人的云。

本文部署的是 Docker 版（core），单个容器，官方镜像，没有真设备也能先跑起来：它自带一个「演示」集成，灯、开关、空调、媒体播放器都是假的，但能点，用来熟悉界面刚好。

项目地址：[点我跳转](https://github.com/home-assistant/core)

## 部署安装

装在内网之后，出了门就连不上了。想在外面也能看家里的状态、点一下灯，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，不需要公网 IP，也不用改路由器。

### 一、用 Docker Compose 部署

飞牛 NAS 打开 Docker 应用，切到 Compose 标签，点「新增项目」。项目名称填 homeassistant；路径选一个空文件夹。

「来源」选「创建 docker-compose.yml」，把下面这段贴进去：

```yaml
services:
  homeassistant:
    image: "ghcr.io/home-assistant/home-assistant:stable"
    network_mode: host
    volumes:
      - ./config:/config
    environment:
      TZ: Asia/Shanghai
    restart: always
```

从上往下要认几处：镜像用的是官方自己发的 ghcr.io/home-assistant/home-assistant，tag 照官方安装文档写 stable —— 这是滚动的稳定版 tag，不用自己去查版本号，你什么时候照着装，装到的都是当下的稳定版；network\_mode: host 是宿主网络模式，官方文档就是这么写的，容器直接用 NAS 的网络，所以不用写 ports，浏览器敲的端口就是它默认的 8123，而且局域网里的设备发现（mDNS、SSDP）才能正常工作；volumes 里的 config 是配置目录，所有设置都落在项目目录下的 config 文件夹里，备份就备份它；TZ 是时区，自动化的时间按它算。

这份编排里没有任何密钥，管理员账号是第一次打开页面时建的。

勾上「创建项目后立即启动」，点确认。镜像压缩后七百多兆，拉起来要等一会儿，列表里看到「运行中」就好了。容器起来之后它自己还要初始化半分钟。

### 二、用内网穿透开放到公网

容器跑起来之后，直接把它开放到公网，后面建账号、加设备都在公网地址上做。

飞牛的应用中心里直接能搜到 PassNAT 插件，装上，用账号登录一次。

然后在插件里点「创建隧道」：隧道名称随便起一个（比如 homeassist），接入节点和传输协议都留默认；「本地IP」这一栏留空不用填，默认就是 127.0.0.1，插件跑在 NAS 上，回环地址天然指向本机；本地端口填 8123。

建好之后回到隧道列表，点开这条隧道的详情，按一下最左边的电源图标，隧道就连上了，面板里会显示分配到的公网地址。PassNAT 的隧道是 TCP 转发，不是反向代理，Home Assistant 这边不用加 trusted\_proxies 那类配置。

### 三、在公网地址上建账号、加设备

浏览器打开刚才那个公网地址，第一次进来是欢迎页，点「创建我的智能家居」。填姓名、用户名、密码，点「创建账户」——隧道一开就先把账号建好，别让它空着挂在公网上。接下来的位置、分析这两页直接点「下一步」，最后点「完成」就进概览页了。

没有真设备的话，先开它自带的演示集成看看。这个集成不能从界面上加（「添加集成」里点「演示」会提示无法通过用户界面添加），要改一个配置文件：项目目录下的 config/configuration.yaml，在末尾加一行 demo: 就行。飞牛的文件管理没有文本编辑器，所以在电脑上改好这个文件，回到文件管理进 config 目录，上传、选「上传并覆盖」；然后到 Docker 的项目列表，点 homeassistant 右边的「···」，重启。

等它起来再回到概览页，灯、开关、空调、媒体播放器全出来了，点一下灯的开关它就亮，空调的温度也能调。

真设备走同一个入口：「添加集成」里搜你的品牌，米家、飞利浦 Hue、HomeKit 都在，按提示登录或者配对就行。

自动化在「设置」→「自动化与场景」里，点「创建自动化」→「创建新的自动化」，触发条件选太阳落山，动作选开灯，天黑灯就自己亮。

## 几个提醒

一、公网上开着的是你家的控制面板，管理员密码一定用长的随机密码，别用生日、手机号这种。

二、宿主网络模式下，Home Assistant 会在局域网里自动发现打印机、电视这类设备，「设备与服务」页顶上会列出来，不想接的点「忽略」就行。

三、如果以后重建隧道、公网端口变了，浏览器里换成新地址就行，Home Assistant 本身不用改配置。

---

原文：<https://www.passnat.com/posts/home-assistant-nas-smart-home-hub-docker-deployment-intranet-penetration>


## 相关文章

- [家庭鸡尾酒管理系统Bar Assistant部署教程，Docker快速安装配置指南](home-cocktail-management-bar-assistant-docker-deployment-guide.md)
- [AdGuard Home 飞牛 NAS 部署与公网管理](adguard-home-docker-ad-blocking-dns.md)
- [Penpot 开源设计工具自建教程：飞牛 NAS Docker Compose 部署官方镜像，Figma 的开源替代，内网穿透出门手机也能改稿](penpot-self-hosted-deployment-guide-nas-one-click-setup.md)
- [Homepage 自建 NAS 导航首页：飞牛 Docker 部署 + 内网穿透外网访问](homepage-nas-service-dashboard-docker-deployment-intranet-penetration.md)
- [Moments 自建朋友圈：飞牛 NAS Docker 部署 + 内网穿透外网访问](moments-self-hosted-private-timeline-nas-docker-deployment-intranet-penetration.md)
