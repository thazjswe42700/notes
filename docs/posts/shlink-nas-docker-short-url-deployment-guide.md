---
title: "Shlink 自建短链接：飞牛 NAS Docker 部署 + 内网穿透，公网建短链看点击统计"
date: 2025-10-16
description: "用 Shlink 把长网址变成自己域名下的短链接，每次点击都有记录。本文在飞牛 NAS 上用 Docker Compose 部署 Shlink 和它的网页后台，并用帕斯内网穿透一条隧道把短链接和后台开放到公网。"
canonical: https://www.passnat.com/posts/shlink-nas-docker-short-url-deployment-guide
---

## 项目介绍

一长串带参数的网址发到群里，既难看又容易被截断。短链接服务干的就是把它变成一个短地址，比如 你的域名/blog —— 而且每被点一次，后台都记一笔：什么时候点的、用的什么系统和浏览器、从哪儿跳过来的。

Shlink 是这类服务里做得比较完整的开源项目。它本身是一个只有接口的服务端，负责生成短码、跳转和记录访问；配套的 shlink-web-client 是一个网页后台，在里面建短链、打标签、看统计。两样都有官方镜像，所以这篇会起两个容器。

项目地址：[点我跳转](https://github.com/shlinkio/shlink)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装 Shlink，并用 [帕斯内网穿透](https://passnat.com) 把它开放到公网 —— 短链接是要发给外面的人点的，只在家里的网里打得开就没有意义。

这一期的顺序跟平时反过来：**先建隧道，再部署**。因为编排里的 DEFAULT\_DOMAIN 要填短链接的公网域名，没有隧道就写不出这一行。

### 一、先建隧道，拿到两个域名

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。

传输协议选 HTTP，本地 IP 留它默认的 127.0.0.1，本地端口填 3414 —— 这是短链接本身；子域名自己起一个，越短越好，这就是以后短链接的样子；顶级域名在下拉里挑一个（自动解析，不用自己配 DNS）。

然后点「添加端口映射」再加一行：本地端口 3415，这是管理后台，子域名另起一个。

**两行的网页访问用户名和密码都空着。** 短链接是要发给别人点的，加了口令，别人点开先看到的是一个要密码的框。后台那边也不用加 —— 后台只是一个静态网页，真正的数据靠下面的 API key 挡着。

填完点创建隧道，按一下电源，状态转成运行中，详情里那两行就是公网地址。把 3414 那一行的域名记下来，下一步要填。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点新增项目。项目名称填 shlink，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去。

```yaml
services:
  shlink:
    image: shlinkio/shlink:stable
    container_name: shlink
    restart: unless-stopped
    ports:
      - "3414:8080"
    environment:
      DEFAULT_DOMAIN: 你的短链接域名
      IS_HTTPS_ENABLED: "false"
      INITIAL_API_KEY: change-me-to-your-own-key
    volumes:
      - ./data:/etc/shlink/data
    user: "1000:1000"
  shlink-web:
    image: shlinkio/shlink-web-client
    container_name: shlink-web
    restart: unless-stopped
    ports:
      - "3415:8080"
```

这份编排要认三处：

**DEFAULT\_DOMAIN** 填刚才 3414 那一行的公网域名（不带 http://，就是「子域名.顶级域名」那一串）。Shlink 生成的每一条短链接都用它开头。IS\_HTTPS\_ENABLED 写 false，是因为这条隧道走的是 http。

**INITIAL\_API\_KEY** 是第一次启动时建的那把钥匙，一会儿登录后台要用。上面写的 change-me-to-your-own-key 只是个提示，**换成你自己的一串**，越乱越好，只用英文字母、数字和连字符。

**user: "1000:1000" 这一行别删。** Shlink 的镜像默认用一个编号 1001 的用户跑，而飞牛建出来的数据文件夹归你自己的账号，它写不进去 —— 容器日志里是一整屏 Permission denied，列表里一直在「重启中」。加上这一行，让它用 1000 这个账号跑就好了（我这台飞牛上的账号是 1000；你的不一样的话，改成你自己的）。数据库、日志都放在挂出来的 data 文件夹里，以后升级重建也不会丢短链接。

冒号左边的 3414 和 3415 就是刚才建隧道填的两个端口，冒号右边的 8080 是容器里的端口，别改。

最后勾上「创建项目后立即启动」，点确认。两个镜像加起来一百多兆，一会儿就起来了。

### 三、在公网上打开后台，加一台服务器

用 3415 那一行的公网地址打开后台，第一次进来是空的，点「Add a server」：名字随便起；URL 填 http:// 加上 3414 那一行的短链接域名；API key 填编排里你换好的那一串。保存就连上了。

⚠ **别用 shlink-web-client 的环境变量去预填服务器地址和 key。** 那样做它会生成一个 servers.json，任何打开后台地址的人都能直接读到你的 key。在界面里填，key 只存在你自己的浏览器里。换一个浏览器或者换台电脑，要再加一次。

顺带一提：官方有一个现成的在线后台，但它是 https 的，浏览器不允许它去连一个 http 的服务端，所以这里自己起一个后台。

### 四、建一条短链接，发出去，看统计

在后台首页的「Create a short URL」里把长网址贴进来，Custom slug 那一格可以自己起短码，不填就随机生成。点 Save，下面「Recently created URLs」第一行就是新的短链接，旁边有复制按钮。

把它发给朋友，他点开会直接跳到原来那个网址。回到后台，那一行的 Visits 就记上了；点进数字能看到每一次访问的时间，「By context」里还有对方用的系统和浏览器。

### 几点说明

**地理位置统计要另配。** 「By location」那一栏需要 MaxMind 的 GeoLite2 数据库，去 MaxMind 官网注册拿一个 license key，编排里加一行 GEOLITE\_LICENSE\_KEY 就有了；不配的话其他统计照常。

**后台界面是英文的**，但就那么几个按钮，建短链、看统计都在首页。

**机器人的访问会单独标出来。** Shlink 会按浏览器标识判断是不是爬虫，聊天软件抓链接预览那一下通常会被记成 bot，不算进正常访问里。

---

原文：<https://www.passnat.com/posts/shlink-nas-docker-short-url-deployment-guide>
