---
title: "自托管照片管理系统 Immich 部署指南，NAS 快速搭建私人相册库，支持人脸识别和智能备份"
date: 2025-10-16
description: "在飞牛 NAS 上用 Docker 自建照片库 Immich：人脸识别、地图、以图搜图，照片全部落在自己的硬盘上；再用帕斯内网穿透把它开放到公网，人在外面也能翻家里的相册。含拿绝对路径、编排要改的三处、建隧道每一栏怎么填的完整步骤。"
canonical: https://www.passnat.com/posts/immich-nas-self-hosted-photo-management-deployment-guide
---

## 项目介绍

Immich 是一个自己部署的照片库。手机拍的照片自动备份进来，全部落在你自己 NAS 的硬盘上 —— 不用往别人的服务器上传，也不用担心哪天空间到期要续。

它能做的事不少：人脸自己认、自己分组，点一个人就把这个人的照片都挑出来；拍照的地点直接落到地图上；还能按画面里的东西搜 —— 打一个「海」，海边拍的那些就出来了。手机端有官方 App，装上之后设成自动备份，跟用云相册的体感差不多，只是东西在自己家里。

它是四个容器一组：主程序、机器学习、Redis 和一个带向量扩展的 PostgreSQL。人脸识别和以图搜图靠的就是后面这两个，所以它比一般的自建服务吃内存，官方建议给到 6 GB 以上。

项目地址：[点我跳转](https://github.com/immich-app/immich)

## 部署安装

装在自己的 NAS 上之后，它默认只能在家里的网络里打开 —— 而相册恰恰是「人在外面想翻一张给别人看」的时候最想用。我家是移动宽带，没有公网 IP，配 IPv6 又嫌麻烦，所以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里就能直接装到插件，不用另外找工具。下面这套流程在飞牛 NAS 上从头到尾走一遍。

### 动手之前先看两样

一是飞牛的容器服务开着；二是存储空间已经划好，因为照片和数据库都要落在一个真实目录上。这两样具备了再往下走。

### 第一步：建目录，并且拿到它的绝对路径

先打开飞牛的文件管理，点新建文件夹，名字随便起。建好之后右键点它，菜单最底下选「详细信息」，在「位置」那一行右边点「复制原始路径」—— 这串路径下一步编排里要用。

⚠ 面板上显示的是友好名（类似「存储空间 1/admin 的文件」），真正的绝对路径只会进剪贴板，所以这一下一定要点「复制原始路径」，不能照着屏幕抄。这一期的编排必须写绝对路径，不能像别的项目那样用 ./ 开头的相对路径。

### 第二步：准备编排文件

下面这份编排需要你改三处：两处路径、一处口令。

```yaml
name: immich

services:
  immich-server:
    container_name: immich_server
    image: ghcr.io/immich-app/immich-server:v3
    volumes:
      - /vol1/1000/immich-app/library:/data
      - /etc/localtime:/etc/localtime:ro
    environment:
      DB_PASSWORD: 换成你自己的口令
      DB_USERNAME: postgres
      DB_DATABASE_NAME: immich
      TZ: Asia/Shanghai
    ports:
      - '2283:2283'
    depends_on:
      - redis
      - database
    restart: always
    healthcheck:
      disable: false

  immich-machine-learning:
    container_name: immich_machine_learning
    image: ghcr.io/immich-app/immich-machine-learning:v3
    volumes:
      - model-cache:/cache
    environment:
      TZ: Asia/Shanghai
    restart: always
    healthcheck:
      disable: false

  redis:
    container_name: immich_redis
    image: docker.io/valkey/valkey:9
    healthcheck:
      test: redis-cli ping || exit 1
    restart: always

  database:
    container_name: immich_postgres
    image: ghcr.io/immich-app/postgres:14-vectorchord0.4.3-pgvectors0.2.0
    environment:
      POSTGRES_PASSWORD: 换成你自己的口令
      POSTGRES_USER: postgres
      POSTGRES_DB: immich
      POSTGRES_INITDB_ARGS: '--data-checksums'
    volumes:
      - /vol1/1000/immich-app/postgres:/var/lib/postgresql/data
    shm_size: 128mb
    restart: always
    healthcheck:
      disable: false

volumes:
  model-cache:
```

两处 volumes 路径：把冒号左边换成刚才复制的那串路径，一处后面接 /library（照片存这儿），一处后面接 /postgres（数据库存这儿）。

时区：TZ 填 Asia/Shanghai。不填的话相册里的时间会跟实际差八个小时，按时间排序会很奇怪。

数据库口令：DB\_PASSWORD 和 POSTGRES\_PASSWORD 这两处要填成同一个值，并且一定要换成你自己的一串 —— 编排里写的是占位符，别照抄。同理，下面 DB\_USERNAME / POSTGRES\_USER 和 DB\_DATABASE\_NAME / POSTGRES\_DB 也是成对的，改的话两边一起改。

端口：网页和 API 都走 2283。较新版本已经把网页、API、微服务合并进 immich-server 一个容器，早期教程里的 3000 / 3001 不再使用。2283 被别的容器占了就改冒号左边，比如写成 '8283:2283'，右边的容器内端口不要动。

### 第三步：在飞牛上建 Compose 项目

回到 Docker，切到 Compose 这一页，点新增项目。项目名称填 immich。路径点右边那个图标，选中第一步建好的那个文件夹。

来源勾「创建 docker-compose.yml」，把编排整段贴进来，然后滑到最上面逐行核对上面说的三处。勾上「创建项目后立即启动」，点确认。第一次要拉四个镜像，等几分钟；四条都显示在跑，就算起来了。

### 第四步：用内网穿透开放到公网

容器跑起来之后，下一步把它开放到公网。客户端不用去官网下载 —— 飞牛的应用中心里搜 PassNAT 就有，装上，账号登一次，后面就不用管了。

隧道直接在插件里建：点创建隧道，名字随便起；接入节点留「自动分配」，系统会按负载挑一个；本地 IP 这一栏留默认的 127.0.0.1 就行，插件本来就跑在 NAS 上，回环地址天然指向本机；本地端口填 2283。

建完按一下电源键，状态转成运行中，详情里那条「公网连接」就是在外面要用的地址。换一个网络（比如手机流量）打开这个地址，相册照常打开 —— 跟在家里开的是同一个。

### 第五步：第一次打开，先把管理员建掉

用公网地址打开，第一次进来会引导你注册管理员账号，注册完就能开始上传照片了。

这一步别拖：从隧道启动到管理员建好之间，这台实例是无主的，谁先打开谁就能注册成管理员，所以隧道一通电就马上把账号建掉。

## 怎么用

网页端可以直接拖照片进来传。真正省事的是手机：装官方 App，服务器地址填你的公网地址，登进去打开自动备份，以后拍的照片会自己传回家里的 NAS。

人脸识别和以图搜图是后台任务，照片传完要等它跑一阵。跑完之后「人物」页会按人分好组，点一个人就是这个人的全部照片；搜索框直接打中文描述也能搜到画面内容。

地点会从照片自带的拍摄信息里读出来落到地图上，所以从手机相册直接传上来的照片，地图页会自然长出来。

## 一些使用上的细节

四个容器里最吃内存的是机器学习那个。NAS 内存紧张的话，可以等照片传完、后台任务跑完之后再把它停掉，搜索和人脸结果已经存进库里了，不会丢。

数据分两块：照片在你指定路径的 library 目录，数据库在 postgres 目录。备份要两个一起备 —— 只备照片的话，人脸分组和相册这些会没了。数据库目录不要放在网络共享（NFS / SMB）上。

暂时不想让外面访问的话，去 PassNAT 插件里把那条隧道的电源关掉就行，内网照常用，容器不用动。

---

原文：<https://www.passnat.com/posts/immich-nas-self-hosted-photo-management-deployment-guide>


## 相关文章

- [家庭智能照片管理系统Photonix，AI识别分类，NAS一键部署，内网穿透远程访问](photonix-ai-photo-management-nas-deployment.md)
- [Ralbum个人相册管理系统，NAS极简部署，内网穿透外网访问，照片库智能管理](ralbum-personal-photo-album-nas-deployment-intranet-access.md)
- [开源自托管订阅管理系统Wallos 内网穿透远程访问 NAS一键部署完整指南](open-source-self-hosted-subscription-management-system-wallos-internal-penetration-remote-access-nas-one-click-deployment-complete-guide.md)
- [自托管私人相册HomeGallery，AI智能识别，NAS极简部署，内网穿透远程访问](home-gallery-private-photos-ai-recognition-nas-deployment-remote-access.md)
- [自建相册管理系统Photoview，NAS极简部署内网穿透远程访问，摄影师RAW格式完美支持](photoview-nas---raw.md)
