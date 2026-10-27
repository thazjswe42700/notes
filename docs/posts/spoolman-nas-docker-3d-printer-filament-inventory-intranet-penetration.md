---
title: "Spoolman：NAS 上的 3D 打印耗材库存，Docker 部署 + 内网穿透"
date: 2026-10-18
description: "每一卷 3D 打印耗材登记材质、颜色和剩余重量，打印完记一笔用量，按存放位置看哪卷快用完：在 NAS 上用 Docker Compose 部署开源耗材库存工具 Spoolman，用帕斯内网穿透开放到公网，出门也能查，含命名卷数据和用量扣减方法。"
canonical: https://www.passnat.com/posts/spoolman-nas-docker-3d-printer-filament-inventory-intranet-penetration
---

## 项目介绍

Spoolman 是一个给 3D 打印玩家用的耗材库存工具。每一卷耗材登记一次：材质、颜色、线径、整卷净重，放在哪个柜子里。打印完回来记一笔用掉多少，剩下的克数和大概还能打多少米，页面上直接算给你。

它最省事的地方是按存放位置看：哪个柜子里还有多少卷、哪一卷快见底了，一屏看完。快用完的那卷进度条会变红，不用每次翻箱子才知道该补货了。

Spoolman 不主动连打印机，用量是手动记的——按打印切片软件估算的克数记一笔，或者用电子秤称一下毛重。记录都在自己 NAS 的数据库里，不经过别人家的服务器。

项目地址：[点我跳转](https://github.com/Donkie/Spoolman)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：用 Docker Compose 把 Spoolman 装上，再用 [帕斯内网穿透](https://passnat.com) 开放到公网，人在外面也能查家里还剩多少耗材。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 spoolman，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  spoolman:
    image: ghcr.io/donkie/spoolman:latest
    container_name: spoolman
    restart: unless-stopped
    ports:
      - "8000:8000"
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - spoolman_data:/home/app/.local/share/spoolman

volumes:
  spoolman_data:
```

数据用的是具名卷 spoolman\_data，挂在容器的 /home/app/.local/share/spoolman。这个路径是镜像里写死的，别改。用命名卷就不用提前建目录、也不用改属主，卷的权限镜像会自己处理。

端口映射写的是 8000:8000，冒号左边是 NAS 上的端口，建隧道时填的就是它。接口里的时区设成 Asia/Shanghai，记录时间才对得上。勾上「创建项目后立即启动」，点确认。

容器起来之后，用 NAS 的局域网地址加 8000 端口打开，就是耗材库存页。

### 二、把耗材录进目录

第一次打开是空的。右上角「添加耗材」，可以从内置的 SpoolmanDB 里搜现成的耗材规格，也可以点「创建新耗材」自己填。自己填的时候，名称、材质、线径、密度、整卷净重这几项按耗材包装上的写；颜色点一下色块选，挤出机温度和热床温度照耗材说明填。

规格建好之后，再给它加一卷：初始净重填整卷的克数，线盘重量填空盘有多重（之后用电子秤称毛重时要用到），存放位置写柜子名或者打印机旁边。每一卷都有自己的编号，同一款耗材买了几卷就建几卷，位置可以各不相同。

### 三、打印完记一笔用量

点开某一卷的详情，上面是大字的剩余克数，下面是进度条和「已使用」的重量。要扣减的时候点「调整用量/重量」，展开的框里有三个页签：按长度、按重量、实测毛重。切片软件给的是克数就选「按重量」，填消耗的克数，点应用；只知道用了多少米就选「按长度」；手边有电子秤，选「实测毛重」称一下整卷，剩下的自动算出来。

扣减之后剩余重量、已使用重量、上次使用时间一起更新。低于一定比例的那一卷，进度条会变成红色，在列表里一眼就能看到该补货的还是够打的。

### 四、在外面打开

耗材库存这种表，最有用的时刻其实是在外面：逛耗材店之前先看一眼家里那卷还剩多少，或者在公司想确认某个颜色够不够打。在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 8000。

建好点开隧道、按一下电源，状态变成「运行中」，详情里那一串就是公网地址，复制下来。用手机打开这个地址，看到的就是同一份库存数据。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 几点说明

**数据和升级。** 所有记录都在 spoolman\_data 这个卷里的 spoolman.db 一个文件里。升级之前先把卷备份一份，再到 Docker 里重新拉取 latest 镜像、重启项目，数据不会丢。

**耗材库自动同步。** 第一次启动时它会从 SpoolmanDB 拉一份公共耗材目录，里面有几千种耗材规格，按品牌和材质搜得到。自己建规格也完全可以，目录里没有的耗材就自己填。

**标签和二维码。** 详情页可以打印标签，把编号和二维码贴在线盘上，扫一下就能跳回这一卷的记录；要不要用看你的习惯，不打印也不影响记录。

---

原文：<https://www.passnat.com/posts/spoolman-nas-docker-3d-printer-filament-inventory-intranet-penetration>


## 相关文章

- [Manyfold：NAS 上的 3D 模型库，Docker 部署 + 内网穿透](manyfold-nas-docker-3d-model-library-preview-penetration.md)
- [开源低代码工具ToolJet快速部署指南，NAS极简搭建内部应用开发平台](open-source-lowcode-tooljet-quick-deployment-guide-nas-simple-setup-internal-app-development-platform.md)
