---
title: "LubeLogger 保养记录：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源车辆保养记录 LubeLogger：保养、加油、里程、税费和提醒都能记，仪表盘自动算今年跑了多少、花了多少、平均油耗；用帕斯内网穿透拿公网地址，在修理厂用手机就能翻上次换的什么件。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/lubelogger-nas-docker-car-maintenance-fuel-records-intranet-penetration
---

## 项目介绍

LubeLogger 是一个开源的车辆保养和油耗记录工具。换过什么件、花了多少、下次该保养到多少公里，都记在自己家的 NAS 上，不用翻手机相册里那几张皱巴巴的维修单，也不担心哪天换个 App 记录全没了。

一台车一张档案：保养、维修、改装、加油、里程、税费都能记；仪表盘把今年跑了多少、花了多少、平均油耗多少算成一屏；提醒按「时间 + 里程」双条件盯着，快到点了在首页顶上一排提示。

项目地址：[点我跳转](https://github.com/hargata/lubelog)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 LubeLogger 用 Docker Compose 装起来。它没有必须预填的公网地址字段，先装再建隧道也完全没问题；本文按「先穿透、后部署」写，是因为最后要在修理厂用手机打开它。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8001（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 lubelogger，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  lubelogger:
    image: ghcr.io/hargata/lubelogger:latest
    container_name: lubelogger
    restart: unless-stopped
    environment:
      - TZ=Asia/Shanghai
      - LANG=zh_CN.UTF-8
      - LC_ALL=zh_CN.UTF-8
    ports:
      - "8001:8080"
    volumes:
      - ./data:/App/data
      - ./keys:/root/.aspnet/DataProtection-Keys
```

这段编排没有要你改的密钥，但有两处值得知道：

**两个挂载目录。** ./data 是数据库、图片和配置，备份它就等于备份全部记录；./keys 是登录用的加密钥匙，不挂的话容器一重建，之前登录过的手机全部掉线，得重新登一次。

**两行 locale。** LubeLogger 的金额符号和日期格式跟着系统 locale 走，不设的话金额会显示成一个「¤」，设成 zh\_CN.UTF-8 之后才是「¥」和 2026/10/12 这样的日期。

端口 8001 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像下完就起来了。

### 三、第一次打开：先改成公制，再设个密码

用公网地址打开，第一次进来没有登录页，直接就是车库——这是它默认的「无密码」状态，自己家里用没问题，但只要开过隧道就必须补上密码。

**改公制。** 右上角 Settings，找到「Use Imperial Calculation for Fuel Economy Calculations(MPG)」，把勾去掉。之后输入里程按公里、加油按升，油耗显示成 l/100km。

**设密码。** 同一页往下拉，勾上「Enable Authentication」，弹窗里填一个用户名和密码，点 Setup。这一步建的是 root 账号，之后再用这个账号登录，所有车都归它管。

想给家里另一个人单独开账号的话，要先在 Settings 里配好发信邮箱（SMTP），再到 Admin Panel 里生成邀请令牌；没配 SMTP 也能手动把令牌抄给对方，不影响主流程。

### 四、把车加进来

车库页上那个虚线加号卡就是新增车辆。填年份、品牌、车型，车牌可填可不填，保存之后车库里就有一张车卡。

点进车卡，默认落在 Dashboard：最上面一排是当前里程、今年跑的距离、总花费、平均油耗，下面四张图分别是支出构成、每月花费和里程、提醒状态、每月油耗。数据是空的，所以一开始这几张图都是空的，记两笔就活了。

### 五、平时怎么记

**保养。** 进 Service Records，点 Add Service Record：日期、当时的里程、做了什么（比如「更换机油机滤 全合成 5W-30」）、花费、备注。弹窗里还有个「Add Reminder」勾选框，勾上就能顺手把下次保养的提醒一起建了。

**加油。** 进 Fuel，点 Add Gas Record：日期、里程、加了多少升、花了多少，勾「加满」。加满的意义是让相邻两次之间的里程差可信，油耗才算得准；偶尔少加一次就在下一条勾上 Missed Fuel Up，系统会跳过那一段。

**提醒。** 进 Reminders，按时间、按里程或者两个条件一起盯。比如机油「5000 公里或 6 个月」：建一条 Metric 选 Both 的提醒，填写下次的里程和日期，再打开循环，之后每次保养完它自己往后推。

**里程。** 平时不做保养也可以单独记一条 Odometer，仪表盘上的「今年跑了多少」就是用它算的。第一条里程记录记得把 Initial Mileage 填上，否则它会把第一条的读数当成一整段距离。

### 六、在外面用

手机浏览器打开同一个公网地址，登录之后看到的是同一份数据：在修理厂等车的时候翻一下上次换的什么件、花了多少，店员报价的时候心里有底；在加油站想不起来上次加了多少升，翻一下加油记录就知道这箱油跑得正不正常。

家里人一起记也简单：把账号密码告诉对方，或者按上面说的另开一个账号，两台手机记的是同一台车。

### 几点说明

**数据就是一个 SQLite 文件。** 在 data/cartracker.db，连同 images 目录一起备份即可；换机器把 data 目录拷过去就能接着用。

**不配 SMTP 的影响。** 只有邀请家人、找回密码这两件事受影响，其余功能全部正常。

**明文 HTTP。** 通过隧道访问是 HTTP，密码是明文传输的。自己家用问题不大，介意的话可以给隧道换成 HTTPS 域名，或者干脆只在家里内网用、需要的时候再开隧道。

**更新。** 镜像标签是 latest，升级前先把 data 目录备份一份，再到 Docker 里重新拉取镜像、启动项目。

---

原文：<https://www.passnat.com/posts/lubelogger-nas-docker-car-maintenance-fuel-records-intranet-penetration>
