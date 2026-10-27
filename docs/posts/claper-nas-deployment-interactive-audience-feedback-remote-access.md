---
title: "开会讲课现场互动：飞牛NAS 部署开源 Claper，台下提问投票、结果实时上大屏"
date: 2025-10-16
description: "Claper 是开源的现场互动工具：台下扫码进来，不用注册就能提问、投票，结果实时上大屏。本文在飞牛 NAS 上用 Docker Compose 部署 Claper，并用帕斯内网穿透开放到公网，含 BASE_URL、关闭开放注册、改默认管理员口令这几处必改项。"
canonical: https://www.passnat.com/posts/claper-nas-deployment-interactive-audience-feedback-remote-access
---

## 项目介绍

Claper 是一个开源的现场互动工具，开会、讲课、做分享的时候用。讲的人建一个活动，台下的人用手机扫码或者打开链接、输活动码进来，**不用注册**，起个昵称就能发问题、点一下就能投票，结果实时出现在讲的人那一屏上，接到投影上大家一起看。

除了提问和投票，它还能发表单、做小测验，也可以把讲稿 PDF 传上去，翻页的时候台下同步跟着翻。界面没有中文，但按钮不多，文末附了一张对照。

项目地址：[点我跳转](https://github.com/ClaperCo/Claper)

## 部署安装

台下的观众用的是自己手机的流量，家里的局域网地址他们打不开。我家是移动宽带，没有公网 IP，所以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网 —— 飞牛的应用中心里就能直接装到插件。这个工具的编排里要填公网地址，所以顺序是：先建隧道拿到地址，再部署，最后通电。

### 一、先建隧道，先别通电

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起，接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 3418（就是下面编排里冒号左边那个）。

🔴 **建好之后先别按电源。** 点开这条隧道的详情，把「公网连接」那一格的地址记下来 —— 这个地址在隧道还没启动的时候就已经分配好了，下一步编排里要用。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目，项目名称填 claper。路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去。

```yaml
services:
  app:
    image: ghcr.io/claperco/claper:latest
    container_name: claper
    restart: unless-stopped
    ports:
      - "3418:4000"
    environment:
      BASE_URL: http://换成你的公网地址
      SECRET_KEY_BASE: 换成你自己的一长串乱码，至少六十四个字符，别照抄
      DATABASE_URL: postgres://claper:claper@db:5432/claper
      ENABLE_ACCOUNT_CREATION: "false"
      PRESENTATION_STORAGE_DIR: /app/uploads
    volumes:
      - ./uploads:/app/uploads
    depends_on:
      db:
        condition: service_healthy
  db:
    image: postgres:15-alpine
    container_name: claper-db
    restart: unless-stopped
    environment:
      POSTGRES_USER: claper
      POSTGRES_PASSWORD: claper
      POSTGRES_DB: claper
    volumes:
      - ./db:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "claper"]
      interval: 5s
      retries: 10
```

这份编排要认四处，从上往下：

**3418:4000** —— 冒号左边的 3418 是 NAS 上对外的端口，建隧道填的就是它；右边的 4000 是容器里的端口，别改。

**BASE\_URL** —— 换成刚才记下的那个公网地址（http:// 开头，带端口）。观众扫码进来的链接、讲的人那一屏上显示的地址，全是照这一行生成的。填成局域网地址的话，台下的人扫出来的是一个他们打不开的地址。

**SECRET\_KEY\_BASE** —— 换成你自己随手敲的一长串乱码，至少六十四个字符，别照抄。它用来签登录状态，谁拿到它谁就能伪造登录。

**ENABLE\_ACCOUNT\_CREATION** —— 设成 false，把开放注册关掉。它默认是开的：不关的话，任何拿到地址的人都能自己注册账号、在你的 NAS 上建活动。关了之后只有你自己能建活动，台下的人进来参与不需要账号，不受影响。

另外两个容器：app 是 Claper 本身，db 是它用的 PostgreSQL。数据库的端口没有映射出来，只在两个容器之间用，所以那组 claper/claper 不对外。数据落在项目文件夹下的 ./db 和 ./uploads 两个目录里，备份就是拷这两个文件夹。

勾上「创建项目后立即启动」，点确认。两个镜像加起来七百来兆，要拉一会儿，拉完列表里会显示正在运行。

### 三、通电，第一件事改默认口令

回到 PassNAT 插件，按一下这条隧道的电源，状态转成「运行中」，公网就通了。

🔴 **Claper 第一次启动时会自动建一个默认管理员：账号 admin@claper.co，口令 claper。** 这个默认口令是公开的，写在它的源码里 —— 所以通电之后第一件事，用公网地址打开，拿这组账号登进去，进设置（Settings）把口令改成你自己的。改完用旧口令再登一次试试，应该是登不进去的。

### 四、建一个活动

登进去是「My Events」活动列表，点「Create an event」，右边滑出一个抽屉。「Event name」是活动名称，随便起，**至少 5 个字**（少了表单会拦住）；「Join code」是活动码，台下的人要输的就是它，它会先给你生成一个，也可以改成好记的；开始时间留默认就行。讲稿 PDF 可以先不传，点最下面的「Create event」建好。

### 五、加一个投票，把它打开

点活动卡片上的「Event Manager」进管理页。左边「Interactions」里点「Poll」，弹窗里填「Poll's title」（题目）和两个选项（Choice 1 / Choice 2，要更多点「+ Add」），点「Create a poll」。

🔴 **新建的投票默认是关着的**，台下看不到它 —— 在左边那一行投票右边，把开关打开。同一个地方还能加表单（Form）和小测验（Quiz），用法差不多。

### 六、大屏上要显示什么，右边两个开关

管理页右边有两块设置，这两个默认都是关的，要让大屏上看得到就打开：

**Show messages**（在「Presentation Settings」里）—— 台下发来的问题显示在大屏上。

**Show results on presentation**（在「Current Interaction Settings」里，打开了投票才有这一项）—— 投票结果显示在大屏上。

开关旁边的文字会跟着状态变：打开之后显示成「Hide messages」「Hide results on presentation」，意思是「点一下就隐藏」，别被绕晕。「Show instructions to join」打开的话，大屏上会出二维码和活动码，开场的时候开着方便大家扫码进来。

### 七、台下的人怎么进来

台下的人用手机打开你的公网地址，在「Join the event」那一页输活动码，点「Join」；也可以直接扫大屏上的二维码。不用注册 —— 点输入框左边那个圆形头像，选「Set your nickname」起个昵称（也可以选「Anonymous」匿名），在「Ask, comment...」那个框里写问题发出去；投票就在页面最上面，点一个选项，再点「Vote」。

### 八、讲的人这边看结果

管理页顶上点「Open presentation」打开大屏，问题一条条上来，投票结果实时出柱状图。这一屏接到投影上，大家一起看；管理页留在自己电脑上控场。

### 按钮对照

Create an event：建活动　Event name：活动名称　Join code：活动码　Event Manager：管理页　Poll：投票　Create a poll：建好投票　Form：表单　Quiz：小测验　Show messages：大屏显示留言　Show results on presentation：大屏显示投票结果　Open presentation：打开大屏　Join the event：加入活动　Set your nickname：起个昵称　Anonymous：匿名　Ask, comment...：提问框　Vote：投票

### 几点说明

**台下的人不需要账号。** 关掉开放注册只影响「谁能建活动」，不影响观众参与。

**编排里的数据库口令只在容器之间用。** db 没有映射端口，外面连不到它；真要改的话，POSTGRES\_PASSWORD 和 DATABASE\_URL 里那一段要一起改。

**它新版本带实时字幕，要接外部的语音识别服务、填对方的 key。** 这篇没讲这个，不配也不影响上面这些功能。

---

原文：<https://www.passnat.com/posts/claper-nas-deployment-interactive-audience-feedback-remote-access>


## 相关文章

- [台下扫码就能提问、投票：飞牛NAS 用 Docker 部署 Claper，内网穿透开放到公网](claper-interactive-presentation-docker-deployment-nas-reverse-proxy-remote-access.md)
- [Lychee 自建私人相册：飞牛 NAS Docker 部署 + 内网穿透外网访问](lychee-nas-self-hosted-photo-album-share-link-password.md)
- [自建网盘 Cloudreve：飞牛 NAS 一个容器就能装，文件传上去右键甩个链接，配内网穿透在外面也能收发](nas-self-hosted-cloud-storage-docker-cloudreve-intranet-penetration-remote-file-share.md)
- [Remark42 自建评论系统：NAS Docker 部署 + 匿名登录 + 嵌入网页完整步骤](remark42-nas-docker-self-hosted-comment-system.md)
- [自托管URL缩短器Pckd：免费开源短链服务，NAS一键部署快速追踪链接数据](pckd-self-hosted-url-shortener-free-open-source-short-link-service-nas-one-click-deployment-fast-tracking-link-data.md)
