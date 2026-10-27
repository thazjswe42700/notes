---
title: "Moments 自建朋友圈：飞牛 NAS Docker 部署 + 内网穿透外网访问"
date: 2026-09-19
description: "一个容器搭一个只属于自己的时间线，发的图文和数据库都在 NAS 的一个文件夹里。本文在飞牛 NAS 上用 Docker Compose 部署 Moments，并用帕斯内网穿透开放到公网，含默认口令和密钥这两处必改项。"
canonical: https://www.passnat.com/posts/moments-self-hosted-private-timeline-nas-docker-deployment-intranet-penetration
---

## 项目介绍

Moments 是一个自己部署的「朋友圈」：发图发文字，按时间倒着排，自己是唯一能发的人，来看的人可以评论。它不是社交网络，也不打算是 —— 它更像一个只属于你一个人的时间线，顺手可以给家人朋友开个口子看。

它够轻：\*\*一个容器就跑起来\*\*，用的是文件型数据库，不用另外起一个数据库容器。发的图文和数据库都落在同一个目录里，备份就是把那个文件夹拷走。

项目地址：[点我跳转](https://github.com/kingwrcy/moments)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装 Moments，装完再用 [帕斯内网穿透](https://passnat.com) 把它开放到公网 —— 装在 NAS 上默认只有家里的网能打开，而一个时间线这种东西，多半是想在外面随手记两句、也想让别人点得开。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 moments，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去。

```yaml
services:
  moments:
    image: kingwrcy/moments:latest
    container_name: moments
    restart: unless-stopped
    ports:
      - "3408:3000"
    environment:
      TZ: Asia/Shanghai
      NUXT_JWT_KEY: change-me-to-your-own-random-string
    volumes:
      - ./data:/app/data
```

这份编排要认三处：

冒号左边的 3408 是 NAS 上对外的端口，一会儿浏览器访问、建隧道填的都是它；冒号右边的 3000 是容器里 Moments 自己监听的端口，别改。

NUXT\_JWT\_KEY 这一行，\*\*必须改成你自己的一串字符\*\*。它是用来签发登录凭证的密钥，从 0.2 版本起是必填项，不填服务用不了。上面写的那串 change-me-to-your-own-random-string 只是个提醒，\*\*照抄着不改，等于没设\*\* —— 任何知道这串的人都能伪造你这台的登录态。随便敲一串长一点的字母数字就行，不用有规律。

最后一行的 ./data 是数据目录，你发的图文和数据库都在里面。⚠ 上游给的编排用的是 Docker 的「具名卷」，这里换成了 ./data 这种相对当前目录的绑定挂载 —— 飞牛的 Docker 界面里没有管理存储卷的地方，具名卷建出来之后不好收拾；挂成目录的话，删项目、换机器、拷备份都是普通的文件操作。

勾上「创建项目后立即启动」，点确认。起来之后在同一个局域网里访问 NAS 的地址加 3408 端口，就能看到界面 —— 中文的，不用切语言。

### 二、用内网穿透开放到公网

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起，接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填编排里那个 3408。

🔴 \*\*建好之后先别按电源。\*\* 这一期的顺序有讲究，下一段说为什么。

### 三、先把默认口令改掉，再通电

Moments 的默认用户名和密码是 admin / a123456 —— \*\*这行字就写在项目说明里\*\*，任何人都查得到。所以在把它放到公网上之前，第一件事是改掉它。

先用局域网地址打开，点右侧那一竖排图标的第三个（登录），用默认的用户名密码登进去。⚠ 顺带一提，直接敲 /login 是打不开的，登录入口只有这个图标。

登进去之后点「用户中心」，往下滚到「密码」那一栏，填一个新的，然后保存。那一栏的提示写着「留空则不修改密码」—— 也就是说不填就是不改，填了才会生效。改完再回插件里按电源，这时候公网地址才开始对外。

### 四、发第一条

用公网地址打开，拿刚改的密码登进去，点「发表」，写点什么，发出去。回首页就能看到它按时间排在最上面。

### 几点说明

\*\*评论默认是开着的，而且不用登录就能评。\*\* 对一个给朋友看的时间线来说这挺方便，但它也是公网上唯一的对外入口 —— 不想要的话，在「系统设置」里把「是否启用评论」关掉就行。

\*\*注册默认是关着的\*\*（实测如此），所以不会有人自己注册进来。要是你想给家人开个号，同样在系统设置里打开那个开关，开完记得再关上。

\*\*图片默认存在本地那个数据目录里\*\*。它也支持接对象存储，但那要另外一套账号和密钥，自己用的话本地存着就够，备份也简单。

---

原文：<https://www.passnat.com/posts/moments-self-hosted-private-timeline-nas-docker-deployment-intranet-penetration>


## 相关文章

- [Homepage 自建 NAS 导航首页：飞牛 Docker 部署 + 内网穿透外网访问](homepage-nas-service-dashboard-docker-deployment-intranet-penetration.md)
- [Homarr 自建服务导航页：飞牛 NAS Docker 部署 + 内网穿透外网访问](homarr-self-hosted-dashboard-nas-docker-deployment-intranet-penetration.md)
- [简单图床 EasyImages 自建：飞牛 NAS Docker 部署 + 内网穿透外网访问](easyimage-self-hosted-image-hosting-nas-docker-deployment-intranet-penetration.md)
- [哪吒监控自建服务器探针：飞牛 NAS Docker 部署 + 内网穿透外网访问](nezha-server-monitoring-nas-docker-deployment-intranet-penetration.md)
- [Easy!Appointments 预约排班：NAS Docker 部署 + 内网穿透](easyappointments-nas-docker-booking-page-intranet-penetration.md)
