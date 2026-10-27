---
title: "SyncTV：NAS Docker 同步影院 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署 SyncTV 1.0：PostgreSQL + 密钥配置、创建房间、添加直链或网盘片源，多人同步播放、进度一致，右侧聊天和语音；用帕斯内网穿透拿公网地址，异地朋友用手机流量也能进同一个房间一起看。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/synctv-nas-docker-synchronized-movie-theater-intranet-penetration
---

## 项目介绍

SyncTV 是一个自己搭建的「同步影院」。大家进同一个房间，看同一部片子：有人暂停，所有人一起停；有人拖动进度条，所有人的画面跟着跳；旁边还有文字聊天和语音。它不提供片源，只是把你自己有的视频、直播流或者网盘里的内容，在几个设备之间保持同步播放。

适用场景很具体：异地的两个人约好晚上一起看电影，或者几个朋友各自在家看同一场球。不用共享屏幕，也不用一个人开着语音喊「你等我一下」，进度天然是一致的。

现在的正式版是 Rust 重写过的 1.0.x，和网上早几年那种「单容器 docker run、数据存 SQLite」的旧教程不一样：它需要 PostgreSQL 存数据，启动前还要给它一组密钥。按下面的编排来，第一次就能起来。项目地址：[点我跳转](https://github.com/synctv-org/synctv)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 synctv，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  synctv-db:
    image: postgres:18
    container_name: synctv-db
    restart: unless-stopped
    environment:
      POSTGRES_USER: synctv
      POSTGRES_PASSWORD: ChangeMeSynctv2026
      POSTGRES_DB: synctv
    volumes:
      - synctv_db:/var/lib/postgresql

  synctv:
    image: synctvorg/synctv:latest
    container_name: synctv
    restart: unless-stopped
    depends_on:
      - synctv-db
    ports:
      - "8080:8080"          # 浏览器打开 http://NAS地址:8080
    volumes:
      - synctv_data:/data
    environment:
      SYNCTV_DATA_DIR: /data
      SYNCTV_DATABASE_URL: postgresql://synctv:ChangeMeSynctv2026@synctv-db:5432/synctv
      # 下面这组密钥第一次启动前生成一次，之后不要再改（改了已存的数据解不开）
      SYNCTV_JWT_SECRET: BWsodZPc9X4Kr_7CV7nw-mGKrMMXNR1xoVWgCrIEBGg
      SYNCTV_SECURITY_CREDENTIAL_ENCRYPTION_KEY: b9a6a97741b7d9fce633d59d43430a103ebe3558eecdb0675145916485db35b5
      SYNCTV_SECURITY_TOTP_ENCRYPTION_KEY: 01b326b09927c420e4ad0e676fb1cb5f381b370dbcccfa929a0658ef292bfc36
      SYNCTV_SECURITY_EMAIL_OUTBOX_ENCRYPTION_KEY: c4628214a75b0b39d14dec66f05be650c61aabac21386faa0df0df1e852720af
      SYNCTV_SECURITY_OPAQUE_SERVER_SETUP_SECRET: ipFnTLNdRMrSxd07QqGH_djhklKgCp1NGr1ODId_shE
      SYNCTV_SECURITY_PROXY_SIGNING_KEY: s7eTS0IJ_MGMsKcbI9AWOq5cRt5JrElZU0TCDj8mc0M
      SYNCTV_SECURITY_MEDIA_SWARM_SIGNING_KEY: JVIc0YVV-NX5tOkK2PryN6jNjKmo2NZlPnzNvzlOAxs
      SYNCTV_SECURITY_PROVIDER_SESSION_ENCRYPTION_KEY: lnbkD6jbQrL0ksRpKbyVi-kFSreVtiLRL7Q3ltiq28w
      SYNCTV_SECURITY_LOGIN_DISCOVERY_KEY: Dq_Y7FDRBzfMbK4nDNsybC-JzT2o_lPAeGZizQ_t7Qc
      SYNCTV_SECURITY_WEBAUTHN_ENUMERATION_KEY: n93-fNaCMAtwwV2giMCE6wjRPloQW5dtwdw1zVwZ2go
      SYNCTV_FILE_UPLOAD_TOKEN_SECRET: gW7TEUyUAg2OJcraUHFMGW9vBx3NWrAgVZThOgBpsjU
      # 首次启动自动建管理员，登录后请改密码
      SYNCTV_BOOTSTRAP_CREATE_ROOT_USER: "true"
      SYNCTV_BOOTSTRAP_ROOT_USERNAME: root
      SYNCTV_BOOTSTRAP_ROOT_PASSWORD: ChangeMe2026

volumes:
  synctv_db:
  synctv_data:
```

这段编排有两个服务：

**synctv-db**：PostgreSQL 18，存用户、房间、播放记录。数据在 synctv\_db 卷里，备份它就行。

**synctv**：SyncTV 本体，浏览器从 8080 端口打开。数据在 synctv\_data 卷里。

**密钥那几行别改。** 启动时会校验这些值：JWT 和加密相关的那几个必须是足够长的随机串，第一次启动生成一次就固定下来，以后不要换 —— 换了之后，已经存进去的登录态和加密数据会解不开。编排里给的是一组演示用的固定值，自己用更稳妥的做法是每个值都换成 openssl rand -hex 32 生成的随机串。

数据库口令和第一次登录的管理员口令也在编排里，装完记得改。SYNCTV\_BOOTSTRAP\_CREATE\_ROOT\_USER 让它在第一次启动时自动建管理员 root，密码就是 SYNCTV\_BOOTSTRAP\_ROOT\_PASSWORD 那一行；建好之后这行可以留着，也可以删掉。

勾上「创建项目后立即启动」，等两个镜像拉完。第一次启动 PostgreSQL 初始化、SyncTV 建表，要几十秒到一两分钟，看到容器状态变成 running 再打开页面。

### 一、第一次打开：登录

浏览器打开 http://NAS地址:8080，看到房间列表首页。右上角点「登录」，用编排里的 root 和管理员口令登录。登录后右上角出现「加入房间」「创建房间」两个按钮。

默认情况下服务器关闭了自助注册，也没有开放匿名访客；这是为了不让陌生人随便进来。要给朋友开账号，可以在管理员界面里建，或者用容器里的管理命令：

```bash
docker exec -it synctv synctv user create 用户名 --password 密码
```

只想临时拉人看一场，也可以在房间设置里开放访客，让朋友拿邀请链接直接进。

### 二、建一个房间，把片子加进去

点「创建房间」，填房间名称（比如「周末电影夜」），简介随意，可见性保持「公开房间」，密码保护选「无密码」。创建完自动进入房间：左边是播放器，右边是聊天和播放列表。

点右边的「播放列表」→「添加媒体」。SyncTV 支持几种来源：

**直链**：一个 HTTP/HTTPS 的 mp4 或 HLS 地址。自己压好的片子放在 NAS 的 Web 服务或者网盘直链上，都可以。

**直播拉流**：RTMP 或 HTTP-FLV，适合自己推的直播。

**媒体库**：AList、Emby、Cloudreve、Nextcloud 这类网盘或媒体服务器的目录，配好地址和账号后直接挂进来。

把链接填进「视频链接」，可以一行一个；想改名字就在「视频名称」里填。点「预览」，确认能识别、再点「添加选中项」。片子出现在播放列表里，点一下就开播。

**地址要浏览器能打开。** SyncTV 是把地址交给播放器去拉的，填 127.0.0.1、容器名这类只有服务器自己能访问的地址，预览能过、播放器会报「当前设备无法播放此视频格式」。填 NAS 在局域网里的地址，或者一个真正的公网直链。

**片源自己负责。** SyncTV 不提供任何内容，也不做版权审核。演示和日常使用请用自己拍的、自己压的，或者明确允许公开传播的片源，不要把没有授权的影视资源挂上来。

### 三、一起看：同步、聊天、语音

房间建好后，点顶部的「复制邀请链接」，把链接发给朋友。朋友登录后打开链接就进同一个房间；房间开放了访客的话，不登录也能进。

播放器右上角有一个「播放偏差」的小标签，正常情况下在 0.0s 附近跳动，表示大家和房间状态对得很齐。任何一个人点暂停、继续、拖进度，服务端会把新状态广播给所有人，其他人的画面跟着走。不想被别人的操作打断，可以在房间设置里限制谁能控制播放。

右侧是聊天面板，文字消息实时到达，带已读标记；上面的「语音聊天」点加入就能开麦。聊天里也会出现「谁加入了房间」「切到了哪个片子」这类系统消息，方便对进度。

片源在外网、拉流慢的时候，可以在添加媒体时把「播放线路」选成「优先代理」或「仅代理」，让 NAS 先缓冲再转发给播放器；局域网内直连够快就用「优先直连」。

### 四、在外面也能一起看：给 8080 做内网穿透

上面的地址只在局域网里有效。出门要用，用 [帕斯内网穿透](https://passnat.com) 把 8080 映射出去：飞牛「应用中心」里搜 PassNAT，装上打开，点「创建隧道」。名字随便起，本地地址留默认的 127.0.0.1，本地端口填 8080，建好点开隧道按下电源，详情里就有公网地址。

把公网地址发给朋友，在外面用手机流量也能进同一个房间。不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

注意：穿透出去的是整个 SyncTV 服务，登录页也会暴露在公网。管理员口令一定要改强，不用的房间及时删掉，别把管理权限留给陌生人。

### 几点说明

**数据在哪。** 用户、房间、播放记录都在 PostgreSQL 的 synctv\_db 卷里；SyncTV 自己的运行数据在 synctv\_data 卷。换机器时两个卷一起带走，再带上同一组密钥，数据才能正常解开。

**密钥一旦定了就别换。** JWT、加密 key、OPAQUE secret 这几类值在首次启动后固定；换掉会导致旧登录态失效、加密的 Provider 凭据解不开。升级、迁移之前先备份卷和当前的环境变量。

**升级。** 镜像标签是 latest，升级前先备份两个卷。1.0.x 启动时会自己跑数据库迁移，迁移过程中不要断电，也不要在迁移没完成时反复重启。

**默认是关着门的。** 自助注册默认关闭，访客加入默认关闭，管理员需要在后台显式打开。这是好事：先确认服务本身安全，再决定让谁进来。

---

原文：<https://www.passnat.com/posts/synctv-nas-docker-synchronized-movie-theater-intranet-penetration>
