---
title: "Calibre-Web 自建电子书库：飞牛 NAS Docker Compose 部署，内网穿透在外面也能翻自己的书"
date: 2026-09-07
description: "Calibre-Web 把 EPUB、MOBI 这些电子书收进自己的 NAS，浏览器打开就是一面封面墙，点开直接在网页里读，读到哪它记着。本文用 Docker Compose 在飞牛 NAS 上部署 LinuxServer 镜像，附一个可以直接下载的空书库文件，再用内网穿透开放到公网，人在外面掏出手机就能接着上次那页读下去。"
canonical: https://www.passnat.com/posts/calibre-web-self-hosted-ebook-library-nas-docker-deployment-intranet-penetration
---

## 项目介绍

Calibre-Web 是一个开源的电子书库网页端。买来的、攒下来的 EPUB、MOBI、PDF 全部收进自己的 NAS，浏览器打开是一面封面墙，按作者、出版社、标签、系列分好类，搜书名作者秒出。

点开一本可以直接在网页里读，不用先下到手机上；读到第几页它自己记着，换台设备打开还接着上次那页。也支持把书推送到 Kindle、按格式下载，多个人各自一个账号，各读各的。

书和阅读进度都在自己的 NAS 上，不放在任何第三方书城里，哪天平台下架了、账号没了，书还是你的。

项目地址：[点我跳转](https://github.com/janeczku/calibre-web)

## 部署安装

装在内网之后，出了门就翻不了自己的书了。想在外面也能接着读，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，不需要公网 IP，也不用改路由器。

### 一、用 Docker Compose 部署

飞牛 NAS 打开 Docker 应用，切到 Compose 标签，点「新增项目」。项目名称填 calibre-web；路径这一栏点右边的文件夹图标，挑一个空文件夹选中确认 —— 这个目录必须是空的，里面已经有 docker-compose 配置文件的话，飞牛会提示你所选择的路径已包含配置文件。

「来源」选「创建 docker-compose.yml」，把下面这段整段贴进去，缩进别动：

```yaml
services:
  calibre-web:
    image: lscr.io/linuxserver/calibre-web:latest
    container_name: calibre-web
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Shanghai
    ports:
      - 8083:8083
    volumes:
      - ./config:/config
      - ./books:/books
    restart: unless-stopped
```

要认三处。image 是 LinuxServer 维护的镜像，tag 就照官方文档写的来 —— LinuxServer 的 README 给的就是 latest，照抄就行，这样你装的时候拿到的是当下的版本。

ports 左边的 8083 是你浏览器要敲的端口，右边的 8083 是容器内部的端口，右边别动；左边那个如果跟你 NAS 上别的服务撞了，换成一个没被占用的就行。

volumes 有两个，都落在项目目录下面：config 存 Calibre-Web 自己的设置和账号，books 就是放书的地方，也是下一步那个书库文件要放的位置。environment 里 PUID、PGID 是容器以哪个用户身份读写这两个目录，飞牛上第一个用户就是 1000；TZ 是时区，写 Asia/Shanghai，书的添加时间才是本地时间。

勾上「创建项目后立即启动」，点确认。等列表里这个项目显示「正在运行」就好了。

### 二、放一个空的书库文件进去

这一步别跳过，跳过了打开是一片空白。Calibre-Web 本身不扫目录，它认的是 Calibre 的书库索引文件 metadata.db，而它自己不会凭空建一个 —— 首次运行时如果找不到这个文件，库路径那一步就通不过。

所以先把这个空书库文件下载下来：[metadata.db（空书库，41 万字节）](https://www.passnat.com/api/media/file/metadata.db)，它是 Calibre-Web 上游仓库里自带的那份空库，37 张表、一本书都没有，干净的。

下载完，用飞牛的文件管理进到刚才那个项目目录，把它丢进下面的 books 文件夹里，跟编排里 volumes 那行对上。放好之后 books 目录里应该正好有 metadata.db 这一个文件。

### 三、用内网穿透开放到公网

容器跑起来之后，直接把它开放到公网，后面的配置和验证都在公网地址上做，省得来回切。

飞牛的应用中心里直接能搜到 PassNAT 插件，装上，用账号登录一次。

然后在插件里点「创建隧道」：隧道名称随便起一个（比如 calibreweb），接入节点和传输协议都留默认，协议是 TCP；「本地IP」这一栏留空不用填，默认就是 127.0.0.1，插件跑在 NAS 上，回环地址天然指向本机；本地端口填 8083，就是编排里 ports 左边那个。

建好之后回到隧道列表，点开这条隧道的详情，按一下最左边的电源图标，状态转成「运行中」，面板里那条公网连接地址就是你在外面要用的地址。

### 四、首次打开：先指库路径，再切中文、改口令

浏览器打开刚才那个公网地址，第一屏是登录页，出厂账号是 admin，口令 admin123，这是官方文档里公开写明的默认值，所以待会儿一定要改掉。

登进去第一件事是指库路径：把「Location of Calibre Database」填成 /books，也就是编排里挂进去的那个容器内路径，不是 NAS 上的绝对路径。填完点这一页最下面的保存，出现「Database Settings updated」就成了。

这一步不做，别的页面一个都进不去 —— 无论点哪儿都会被弹回这一页，这是它的设计，不是出错了。

库路径认上之后，界面出厂是英文的。点右上角进个人设置，把 Language 那一项换成「中文 (简体, 中国)」，保存，界面当场就切过来了。

顺手在同一页把口令改掉。这个服务现在开放在公网上，出厂口令一定不能留着。

### 五、把书传进去

Calibre-Web 不扫目录 —— 直接往 books 文件夹里丢 EPUB 它是看不见的，得让它自己写进书库索引。

先进管理页面，把「上传」这个开关打开（出厂是关的）；回到书架，右上角就多了一个上传按钮，点它把书选进来，封面、书名、作者它自己就从文件里认出来了，排到架子上。

之后人在外面，浏览器敲那个公网地址就是自己的书架，点开一本直接在网页里翻。

## 几个提醒

一、公网上开着的是自己的书库，出厂口令务必改掉，用长一点的随机口令。

二、想多个人各读各的，管理页面里可以再建普通账号，每个账号的阅读进度是分开记的。

三、书多了想改书名、封面、分类，在书的详情页点编辑就能改，改完直接写回书库索引。

四、以后重建隧道、公网端口变了，浏览器里换成新地址就行，Calibre-Web 本身不用改任何配置。

---

原文：<https://www.passnat.com/posts/calibre-web-self-hosted-ebook-library-nas-docker-deployment-intranet-penetration>


## 相关文章

- [魔豆文库MOREDOC开源部署指南，Golang企业级文档管理系统，支持多格式文件预览，NAS内网穿透轻松搭建](moredoc-golang-enterprise-document-management-system-multi-format-preview-nas-intranet.md)
- [Stump 电子书库：NAS Docker 部署 + 内网穿透](stump-nas-docker-ebook-library-reading-progress-intranet-penetration.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [自托管即时通讯平台Stoat完整部署指南，内网穿透远程访问无公网限制](self-hosted-stoat-instant-messaging-deployment-guide-intranet-penetration-remote-access-no-public-ip.md)
- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
