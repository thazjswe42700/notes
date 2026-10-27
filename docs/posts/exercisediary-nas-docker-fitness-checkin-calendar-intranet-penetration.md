---
title: "ExerciseDiary 健身打卡：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源健身打卡 ExerciseDiary：先建动作，再按天记重量和次数，首页一张绿色日历把坚持的日子全画出来，Stats 看每个动作的次数变化，体重单独一页；用帕斯内网穿透拿公网地址，出差在酒店也能把当天训练补上。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/exercisediary-nas-docker-fitness-checkin-calendar-intranet-penetration
---

## 项目介绍

ExerciseDiary 是一个开源的健身打卡日历，解决的问题很具体：练没练、练了多少，过一阵子自己都记不清。它把每一笔训练按日期记下来，首页用一张热力图样的日历把练过的日子涂成绿色，坚持了多久一眼就看到。

用法是两步：先在「Add」里把常练的动作建好（分组、名字、默认重量和次数），以后每次练完，回到首页在当天的日期下面加几行、填重量和次数、点保存就行。动作可以按「力量」「有氧」分组，首页左边按组折叠。

数据存在一个 SQLite 文件里，整个应用就是一个容器，备份一个卷就够了。

项目地址：[点我跳转](https://github.com/aceberg/ExerciseDiary)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 ExerciseDiary 用 Docker Compose 装起来。先穿透、后部署，装完直接用公网地址打开，以后出差、在健身房，手机也能把当天的训练补上。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8850（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 exercise，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  exercisediary:
    image: ghcr.io/aceberg/exercisediary:latest
    container_name: exercisediary
    restart: unless-stopped
    ports:
      - "8850:8850"
    environment:
      - TZ=Asia/Shanghai
      - PORT=8850
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - exercisediary_data:/data

volumes:
  exercisediary_data:
```

这段编排里没有要改的密钥或口令，端口 8850 直接对上前面隧道里填的本地端口。镜像默认监听 8851，所以编排里用 **PORT=8850** 把它固定成 8850，和宿主机映射、隧道三处保持一致；以后想换端口，这三个地方一起改。

勾上「创建项目后立即启动」，镜像下完就起来了。第一次启动它会自己建配置文件和空数据库，十几秒就好，打开就是一个空日历。

数据都在 exercisediary\_data 这个具名卷里（容器里的 /data/ExerciseDiary/sqlite.db），备份这一个卷就够了。

### 三、第一次打开：建动作、记今天

用公网地址打开，顶部一排是 Add、Stats、Weight、Config。先去 **Add** 建动作：Group 填分组（比如「力量」），Place in group 填排序，Name 填动作名（比如「深蹲」），Weight (default) 和 Reps (default) 填你常用的重量和次数，保存。多建几个，分组一样的会收在首页左边同一个组里。

回到首页，右边是当天的记录表。日期用左右箭头翻，表里一行一个动作，填 Name、Weight、Reps，点 **Save** 就记下了。也可以点左边分组展开，直接把整组动作加进当天表格，再改数字。

每保存一天，左上那张日历就多一格绿色；练得多的一天颜色更深，空着的日子还是灰格子。

### 四、看进步：Stats、体重、登录

**Stats**：下拉选一个动作，右上柱状图是每次的次数，右下折线是这个动作的重量变化，左边是逐条记录，可以翻页。比如深蹲的次数和重量，练了两个月有没有涨，一眼能看出来。

**Weight**：体重单独一页，一天记一个数，上面是折线图。

**Config**：主题、深浅色、热力图颜色、每页条数都在这里。想只给自己看，把 Auth 里的 **Enable** 打开，填 Login 和 New password，保存；之后打开地址会先要账号密码。这个开关管的是「谁能看你的打卡记录」，不是传输加密。

### 几点说明

**数据在哪。** 全部在一个 SQLite 文件里，位置是卷 exercisediary\_data 下的 /data/ExerciseDiary/sqlite.db。想搬家，把这个卷导出来带走就行。

**升级。** 镜像标签用的是 latest，升级前先把卷备份一份，再到 Docker 里重新拉取镜像、重启项目。

**它不是什么。** 这是自己记、自己看的打卡本，不连手表、不连手环，也没有手机 App；在手机上用浏览器打开同一个地址就是全部用法。

---

原文：<https://www.passnat.com/posts/exercisediary-nas-docker-fitness-checkin-calendar-intranet-penetration>
