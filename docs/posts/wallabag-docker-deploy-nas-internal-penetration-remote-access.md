---
title: "Wallabag 稍后读：飞牛 NAS Docker 部署 + 内网穿透外网访问"
date: 2025-10-16
description: "看到好文章没空读，把网址贴进去，Wallabag 把正文存好、去掉广告，排成干净的阅读页。本文在飞牛 NAS 上用 Docker Compose 部署 Wallabag，用帕斯内网穿透开放到公网，含 DOMAIN_NAME 要先建隧道、默认口令马上改这两处必看项。"
canonical: https://www.passnat.com/posts/wallabag-docker-deploy-nas-internal-penetration-remote-access
---

## 项目介绍

Wallabag 是一个自己部署的稍后读网站：看到好文章没空读，把网址贴进去，它会把正文抠出来存好，去掉广告和弹窗，排成干净的阅读页，什么时候想读什么时候翻。

它跟收藏夹不一样：收藏夹存的是网址，原网页哪天打不开、改版、删文了，这条收藏就废了；Wallabag 存的是抓回来的**正文**，原站没了你这份还在。已读、星标、标签都能标，文章多了也找得到。数据全存在你自己的 NAS 上。

项目地址：[点我跳转](https://github.com/wallabag/wallabag)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装 Wallabag，再用 [帕斯内网穿透](https://passnat.com) 把它开放到公网。

⚠ **顺序跟一般的项目反过来：先建隧道，再部署。** Wallabag 编排里的 SYMFONY\_\_ENV\_\_DOMAIN\_NAME 要填你访问它用的那个地址，页面里的样式文件、脚本和图标全是按它拼出来的绝对地址。填成内网地址，从公网打开时这些文件都会去找内网，页面就加载不出来。所以要先拿到公网地址，编排里才填得对。

### 一、先建隧道，拿到公网地址（先别通电）

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 3500（下面编排里对外的那个端口）。

建好之后点开这一行的详情，就能看到分配好的公网地址（形如 IP:端口），把它记下来。**先别按电源** —— 容器还没装，现在通电没有意义。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 wallabag，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去。

```yaml
services:
  wallabag:
    image: wallabag/wallabag:latest
    container_name: wallabag
    restart: unless-stopped
    network_mode: bridge
    ports:
      - "3500:80"
    environment:
      SYMFONY__ENV__DOMAIN_NAME: http://换成隧道的公网地址
      SYMFONY__ENV__LOCALE: zh
      SYMFONY__ENV__FOSUSER_REGISTRATION: "false"
    volumes:
      - ./data:/var/www/wallabag/data
      - ./images:/var/www/wallabag/web/assets/images
```

要改的只有一处：**SYMFONY\_\_ENV\_\_DOMAIN\_NAME 换成第一步记下来的公网地址**，写成 http://IP:端口 的样子，末尾不要带斜杠。

另外几处认一下：冒号左边的 3500 是 NAS 上对外的端口，建隧道填的就是它，冒号右边的 80 是容器里自己监听的端口，别改；SYMFONY\_\_ENV\_\_LOCALE 写 zh，界面就是简体中文（不写默认是 en）；FOSUSER\_REGISTRATION 设成 false 是关掉公开注册，别人没法在你的站上自己注册账号。

数据都在项目文件夹里：./data 里是数据库（一个 SQLite 文件），./images 里是存下来的文章配图。备份就把这个文件夹整个拷走。

勾上「创建项目后立即启动」，点确认。镜像下完就能起来，起来之后端口就通了。

### 三、通电，马上改默认口令

回到 PassNAT 插件，点开这条隧道按一下电源，状态转成运行中，公网地址就通了。

🔴 **紧接着用公网地址打开，把默认口令换掉。** Wallabag 出厂只有一个账号，用户名和密码都是 wallabag，这是写在官方文档里的公开口令，隧道一开公网上谁都能试。登录之后点右上角的人像图标，选「配置」，切到「密码」这个标签：现有密码填 wallabag，新密码至少 8 个字符，再输一遍，点保存，看到「密码已更新」就行。

### 四、存第一篇文章

点右上角的加号，把想存的文章网址贴进去，回车。Wallabag 会自己去那个网址把正文抓回来，几秒之后列表里就多了一张卡片。抓取是 NAS 自己去访问那个网址，所以 NAS 能打开的网页才存得进来。

点开这张卡片就是阅读页：标题、来源、预计读多久放在上面，正文是去掉广告和弹窗之后的干净排版。左边那一排按钮，从上往下有标已读（对勾）、加星标、删除、打标签，再往下是切换主题、分享、打印这些。

### 五、文章多了怎么找

左边栏的未读、收藏、存档、标签，分别把对应的文章列出来；右上角的放大镜可以搜标题和正文里的字。文章打上标签之后，点一下标签就能把同一类的拉出来看。

手机上直接用浏览器打开同一个公网地址就行，页面会自己适配。Wallabag 还有配套的手机 App 和浏览器扩展，需要的时候到「API 客户端管理」里建一个客户端再填进去，这里不展开。

### 几点说明

**全程用公网地址访问。** DOMAIN\_NAME 填的是公网地址，页面里的样式和脚本都按它去找，所以隧道没通电的时候，用局域网地址打开页面是没有样式的样子，这不是坏了，通电后用公网地址打开就好。

**想更稳妥一点。** 编排里没写 SYMFONY\_\_ENV\_\_SECRET，用的是镜像自带的默认值；想再加一层，可以自己加一行，换成你自己生成的随机字符串，写进去之后别随便再改。

**更新。** 镜像用的是官方写法的 latest，升级之前把 ./data 整个备份一份，再到 Docker 里重新拉取镜像、启动项目。

---

原文：<https://www.passnat.com/posts/wallabag-docker-deploy-nas-internal-penetration-remote-access>
