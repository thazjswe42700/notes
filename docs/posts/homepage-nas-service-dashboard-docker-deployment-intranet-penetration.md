---
title: "Homepage 自建 NAS 导航首页：飞牛 Docker 部署 + 内网穿透外网访问"
date: 2026-09-07
description: "一个容器把散在各个端口的自建服务收成一个导航首页，面板内容全由 yaml 配置文件决定。本文在飞牛 NAS 上用 Docker Compose 部署 Homepage，并用帕斯内网穿透开放到公网。"
canonical: https://www.passnat.com/posts/homepage-nas-service-dashboard-docker-deployment-intranet-penetration
---

## 项目介绍

NAS 上装的东西一多，入口就散了：这个 3001、那个 5230，端口全靠记。Homepage 就是来收口的 —— 一个页面把所有服务摆成卡片，点一下直接进，哪个服务在线、响应多快，卡片上直接显示。

跟同类工具比，它最大的特点是没有拖拽编辑器：面板长什么样完全由几个 yaml 配置文件决定。想加一张卡就在文件里多写几行，页面刷新就有 —— 配置能直接备份、能塞进 git，换台机器照搬过去就是同一个面板。

项目地址：[点我跳转](https://github.com/gethomepage/homepage)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装 Homepage，并用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，人在外面也能打开这一页、一眼看清家里的服务都在不在线。

⚠ 顺序跟别的教程不一样：**先建隧道，再部署**。因为 Homepage 有主机名校验，编排里要填公网地址，而这个地址得先建好隧道才拿得到。

### 一、先建隧道，拿到公网地址

在飞牛的应用中心装上 PassNAT 插件，打开它，直接在插件里新建一条隧道：名字随便起，传输协议选 TCP，本地地址留空（默认就是 127.0.0.1），本地端口填 3200。

建好之后按一下电源启动，隧道详情里会给出一个公网地址，形如 IP 加端口。**把它记下来，下一步编排里要填。**

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 homepage，路径选一个空文件夹。

「来源」选「创建 docker-compose.yml」，然后把下面这段编排整个贴进去。

### 三、编排文件

```yaml
services:
  homepage:
    image: ghcr.io/gethomepage/homepage:latest
    container_name: homepage
    ports:
      - 3200:3000
    volumes:
      - ./config:/app/config
    environment:
      - HOMEPAGE_ALLOWED_HOSTS=建好隧道后把公网地址填这里
    restart: unless-stopped
```

### 三处需要认一下

**端口 3200**：冒号左边是 NAS 上对外的端口，被占了就改成别的，跟刚才建隧道时填的本地端口保持一致。

**./config**：配置目录，用的是相对路径，就落在刚才选的那个项目目录下面。容器第一次跑起来会往这儿生成一批 yaml，面板的内容全在里面。

**HOMEPAGE\_ALLOWED\_HOSTS**：把第一步拿到的公网地址填在这里，写成「IP:端口」的样子，不带 http。多个地址用英文逗号隔开、中间不要空格。

填完勾上「创建项目后立即启动」再点创建。镜像七十多兆，拉完很快就跑起来了。之后直接用公网地址打开就能看到面板。

### 四、把面板换成自己的服务

第一次打开是三组示例卡片。真正决定面板内容的是配置目录里的 **services.yaml**。下面这份可以直接用，把里面的地址换成你自己 NAS 上的服务就行：

```
---
- 常用:
    - 飞牛 NAS:
        href: http://192.168.0.46:5666/
        description: 文件、Docker、存储都在这儿
        siteMonitor: http://192.168.0.46:5666/
    - copyparty:
        href: http://192.168.0.46:3923/
        description: 上传下载和在线看图
        siteMonitor: http://192.168.0.46:3923/

- 工具:
    - Gitea:
        href: http://192.168.0.46:3300/
        description: 自己的代码仓库
        siteMonitor: http://192.168.0.46:3300/
    - Memos:
        href: http://192.168.0.46:5230/
        description: 随手记
        siteMonitor: http://192.168.0.46:5230/

- 监控:
    - Uptime Kuma:
        href: http://192.168.0.46:3001/
        description: 帮我盯着这些服务
        siteMonitor: http://192.168.0.46:3001/
```

怎么放上去：把上面这段**存成一个叫 services.yaml 的文件**，在飞牛的文件管理里进到项目目录下的 config，点「上传」→「上传文件」选中它，跳出「同名文件处理」时选「上传并覆盖」。传完回到面板刷新一下，卡片就换过来了。

**文件名一定要是 services.yaml** —— 飞牛上传后的文件名就是你本地的文件名，叫别的名字它会当成一个新文件放进去，面板不会变。

### 五、还能怎么折腾

同一个目录下还有几个文件，写法都一样：

**bookmarks.yaml**：书签区。自带的几条示例用不上的话，把文件内容改成只剩一行三个减号就没了。

**widgets.yaml**：顶栏那条。默认是 CPU、内存、磁盘剩余量加一个搜索框。

**settings.yaml**：标题、配色、每行摆几列这些。

每张服务卡还能加一行 siteMonitor，填服务地址，Homepage 会定时探一下，卡片上就会显示响应时间和在线状态 —— 人在外面打开这一页，家里哪个服务停了一眼就看出来。

---

原文：<https://www.passnat.com/posts/homepage-nas-service-dashboard-docker-deployment-intranet-penetration>
