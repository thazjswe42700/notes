---
title: "自建 Dockhand 容器面板：飞牛 NAS Docker 部署 + 内网穿透"
date: 2026-09-08
description: "NAS 上跑了一堆 Docker 容器，起停、看日志、改编排要来回切工具？在飞牛 NAS 上用 Docker Compose 自建 Dockhand，一个容器就够，容器管理、Compose 可视化编排、应用模板、本地登录都在一个网页里，再用内网穿透在外面也能管。含完整编排文件和逐步说明。"
canonical: https://www.passnat.com/posts/dockhand-docker-management-panel-nas-deployment-intranet-penetration
---

## 项目介绍

Dockhand 是一个 Docker 管理面板。仓库是 2025 年底才建的，很新，但更新很勤，已经六千多颗星。它把容器、Compose 编排、镜像、卷、网络这些东西全收进一个网页里。

它跟同类最不一样的地方是「轻」：装它只要一个容器，数据库默认用自带的 SQLite，不用再配一套外部数据库，树莓派这种小机器也跑得动。

能干的事大致是这些：容器起停、重启、看实时日志、直接开一个终端进容器里；Compose 编排有可视化编辑器，改完直接重新部署；自带一个应用模板目录，想装新东西挑一个模板填两个格子就行；还能从 Git 仓库拉编排来部署。

它自带本地登录（也支持 OIDC），但出厂是关着的 —— 这一点后面第五步会专门说怎么开。

项目地址：[点我跳转](https://github.com/Finsys/dockhand)

## 部署安装

面板这东西的价值就在于随时能管一下。装在内网，出了门就够不着，那它一半的用处就没了。想省事可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里就能装到客户端，隧道直接在插件里建。

我家是移动宽带，没有公网 IP，拿公网 IP 也没那么简单。为了快速方便，直接用内网穿透是最省事的一条路。

### 第一步：准备编排文件

下面这份可以直接用：

```yaml
services:
  dockhand:
    image: fnsys/dockhand:latest
    container_name: dockhand
    ports:
      # 左边 33334 是 NAS 上开的口，右边 3000 是它自己的
      - 33334:3000
    volumes:
      # 它靠这一行跟 NAS 上的 Docker 说话，容器列表就是从这儿来的
      - /var/run/docker.sock:/var/run/docker.sock
      # 账号和它自己的数据存在这个目录里
      - ./data:/app/data
    restart: unless-stopped
```

这份编排有三处值得单独说：

① ports 那一行 33334:3000。左边 33334 是 NAS 上开出来的口，右边 3000 是 Dockhand 自己的默认端口。左边这个数字你可以随便换成没被占用的，但后面建隧道要填的是它，别填成 3000。

② /var/run/docker.sock 这一行是整份编排里最关键的一行。Dockhand 就是靠它跟 NAS 上的 Docker 说话的，你在面板里看到的容器列表、能点的起停按钮，全都从这里来。没有这一行，面板打开是空的。

③ ./data:/app/data 是它自己的数据目录 —— 账号、设置、面板自己的那份 SQLite 都落在这里，也就是你上一步在文件管理里挑的那个文件夹。这里特意用的是绑定挂载而不是具名卷，好处是你随时知道数据在哪、备份直接拷这个目录就行。

镜像这行说明一下：官方给的安装命令写的就是 fnsys/dockhand:latest，并没有钉某个版本号。所以这里照抄，你什么时候拉，拿到的就是当时的最新版，不用回头来改这份编排。

### 第二步：在飞牛里建 Compose 项目

打开飞牛桌面上的 Docker，切到上面的「Compose」标签，点「新增项目」。

项目名称填 dockhand。路径这里点右边的图标挑一个空文件夹，没有就在选择器里当场新建一个。

「来源」选「创建 docker-compose.yml」，把上面那段贴进去，勾上「创建项目后立即启动」，点确认。

镜像不大，十几秒就拉完起来了。

### 第三步：建隧道开放到公网

飞牛的应用中心里直接搜 PassNAT 就能装到客户端，装完账号登一次就行。

打开插件，进「隧道」，点创建隧道。名字自己起一个，接入节点和传输协议都留默认。

「本地IP」这一栏不用填 —— 留空默认就是 127.0.0.1，插件本来就跑在 NAS 上，回环地址天然指向本机。本地端口填 33334，也就是上面编排里冒号左边那个数。

建好之后在隧道列表里点开详情，按一下最左边的电源图标连接，端口列表里就能看到分配给你的公网地址，浏览器直接敲那个地址就打开了。

### 第四步：第一次进来先加一个「环境」

第一次打开 Dockhand 是空的，页面上写着 No environments configured —— 它还不知道要管哪台机器。

进「Settings」，选「Environments」，点右边的「Add environment」。名字随便起一个，比如 NAS。

连接方式默认就是 Unix socket，路径也已经填好是 /var/run/docker.sock，不用改。点一下「Test connection」看看通不通，再点「Add」。加完左边的 Containers、Stacks 这些页就有东西了。

### 第五步：把登录开起来

Dockhand 出厂是不需要登录的，页面上会直接写着 Authentication is disabled - open access。既然要开到公网，紧接着就把登录开起来。

还是在「Settings」里，切到「Authentication」，先进下面的「Users」子页签，点「Add user」，把用户名、邮箱、显示名和口令填上，点「Create user」。

建好人之后，再把这一页最上面那个总开关打开。顺序别反 —— 先开开关再建人，会把自己关在门外。

开完之后换个浏览器（或者无痕窗口）打开同一个地址，会先要口令，说明生效了。

### 第六步：日常怎么用

Containers 这一页是 NAS 上跑着的所有容器。上面有搜索框，输个名字就只剩它。每一行右边能起、能停、能重启，点开进去还能看实时日志，也能直接开一个终端。

Stacks 这一页是 Compose 编排。点进某个项目就能看到它的 YAML，改完直接重新部署，不用回命令行。

Templates 里是它自带的应用模板目录，想装新东西可以从这里挑一个，填两个格子就部署了。

看完之后在插件里把隧道关掉就行，就点一下电源键那一下。隧道本身还留着，下次再按一下电源键就回来，地址和端口都不会变。

## 常见问题

页面打开是空的、什么容器都没有：多半是环境还没加，或者 docker.sock 那一行没挂进去。先去 Settings 的 Environments 里看看那个环境是不是绿的。

端口打不开：先确认容器起来了，再确认 33334 这个端口没被别的服务占掉。飞牛的 Docker 里看容器状态最快。

第一次打开会弹一个更新日志窗口：那是它的「What's New」，点右下角的 Got it 关掉就行。不想每次都看到，可以在 Settings 的 General 里把 Show "What's New" 关掉。

---

原文：<https://www.passnat.com/posts/dockhand-docker-management-panel-nas-deployment-intranet-penetration>
