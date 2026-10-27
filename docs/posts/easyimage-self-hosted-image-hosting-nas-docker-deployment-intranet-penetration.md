---
title: "简单图床 EasyImages 自建：飞牛 NAS Docker 部署 + 内网穿透外网访问"
date: 2026-09-20
description: "一个容器搭一个自己的图床，不用数据库，传完直接给直链、Markdown、BBCode 三种链接。本文在飞牛 NAS 上用 Docker Compose 部署 EasyImages，并用帕斯内网穿透开放到公网，含两格域名和「登录上传」这几处必改项。"
canonical: https://www.passnat.com/posts/easyimage-self-hosted-image-hosting-nas-docker-deployment-intranet-penetration
---

## 项目介绍

简单图床（EasyImages）是一个自己部署的图床。传图进去，它直接给你三种链接：直链、Markdown、论坛用的 BBCode，点一下就复制。写博客、发帖、往笔记里贴图，要的就是这一步。

它最省事的地方是**不用数据库**。一个容器就跑起来，配置和图片各落在一个目录里 —— 备份就是把那两个文件夹拷走，换台机器把目录挪过去就接着用。

项目地址：[点我跳转](https://github.com/icret/EasyImages2.0)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装简单图床，装完再用 [帕斯内网穿透](https://passnat.com) 把它开放到公网。图床这种东西尤其需要这一步 —— 它产出的就是要发给别人的链接，只在家里的网里打得开的话，等于白传。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 easyimage，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去。

```yaml
services:
  easyimage:
    image: ddsderek/easyimage:latest
    container_name: easyimage
    restart: unless-stopped
    ports:
      - "3407:80"
    environment:
      TZ: Asia/Shanghai
    volumes:
      - ./config:/app/web/config
      - ./i:/app/web/i
```

这份编排要认三处：

冒号左边的 3407 是 NAS 上对外的端口，一会儿浏览器访问、建隧道填的都是它；冒号右边的 80 是容器里 nginx 监听的端口，别改。

./config 是配置目录。后面在后台改的那些开关，最后都落在这里面的配置文件里。

./i 是图片目录，你传上去的图全在里面，按日期分文件夹放着。⚠ 上游给的编排挂的是 /root/data 下面的绝对路径，这里换成了 ./config 和 ./i 这种相对当前目录的绑定挂载 —— 这样整个项目就是一个文件夹，删项目、拷备份、换机器都是普通的文件操作。

另外说一句镜像的来源：这个项目**没有官方出的镜像**，上游 README 里的 Docker 指南点名用的就是编排里这一个（ddsderek/easyimage），所以它是上游自己背书的那一个。

勾上「创建项目后立即启动」，点确认。镜像只有九十来兆，很快就起来。

### 二、先建隧道，但先别通电

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起，接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填编排里那个 3407。

🔴 **建好之后先别按电源，先把公网地址记下来。** 这个地址在隧道还没启动的时候就已经分配好了，下一步的安装向导里要用到它。为什么要这么绕，下一段说。

### 三、走安装向导：两格域名和管理密码

用**局域网**地址打开，它会自己跳到安装向导。第一步是环境检测，一张检查表，全绿就直接下一步。第二步是网站基础配置，这一步有两格要动。

**这一步有两格域名，两格都要换成刚才那个公网地址。** 它们预填的都是你当前访问的那个局域网地址。

**第一格是「网站域名」。** 这一格的影响比看上去大：这个程序**把页面上每一个绝对地址都照它拼出来** —— 样式表、脚本、顶部导航的每一个链接，全是。留着局域网地址的话，你从外面打开这个站，看到的是一张**没有样式、脚本全挂**的白页，连登录都提交不了（口令要靠页面上的脚本先算一道，脚本没加载就交不上去）：浏览器不允许一个公网来源的页面去取你家内网地址上的东西。⚠ 反过来是允许的，所以两格都填公网地址之后，在家里用局域网地址打开也照样是好的，只是那些样式脚本会绕公网走一圈，慢一点。

**第二格是「图片域名」。** **上传之后给出来的外链就是照这一格拼的** —— 留着局域网地址的话，你复制出去的链接发给别人，别人一个都打不开。

**还有「管理密码」。** 它**预填了一个默认口令 admin@123**，而且那两个密码框是明文显示的，你能直接看见。改成你自己的 —— 这是后台的钥匙，也是下一步开关的钥匙。

填完点「开始安装」，它会直接跳到后台登录页。

### 四、把「登录上传」打开，再通电

用刚设的账号登进后台，点「设置」，切到「图床安全」，把里面的**「登录上传」**打开。

🔴 **这个开关默认是关的。** 不打开的话，任何人拿到你这个地址，都能往里传文件 —— 不用登录，不用验证码。而图床的链接是要发出去的，地址迟早会被人看到；别人往里传的东西，会挂在你的域名下、占你 NAS 的盘。所以这一步要放在通电之前做完。

同一页上还有一个「验证码」开关，默认也是关的。只想给自己用的话，开了「登录上传」就够了；要是你打算把注册放开给朋友用，那把验证码也一起开上。

改完回插件里按一下电源，隧道状态转成运行中，这时候公网地址才开始对外。

### 五、传第一张图

用公网地址打开，登进去，选一张图，点开始上传。传完下面就列出三种链接：直链、Markdown、BBCode，右边点一下就复制。注意看链接里的域名 —— 就是刚才在安装向导里填的那个公网地址，这就是为什么那一格要先填对。

### 几点说明

**图片就是普通文件。** 传上去的图按日期分目录放在 ./i 里，想批量拿走、想换个地方接着用，直接拷目录就行，没有导入导出这一说。

**后台设置项挺多，但默认值基本可用。** 水印、缩略图、上传压缩、黑白名单这些都在设置页里，刚装完不用一项项过，先按上面说的把「登录上传」打开就能用。

**它是 PHP 写的，跑在容器里的 nginx 后面。** 所以不需要你在 NAS 上装任何运行环境，编排里那一个容器就是全部。

---

原文：<https://www.passnat.com/posts/easyimage-self-hosted-image-hosting-nas-docker-deployment-intranet-penetration>


## 相关文章

- [DailyTxT 加密日记：飞牛 NAS Docker 部署 + 内网穿透外网访问](private-diary-encryption-nas-local-deployment-dailytxt-intranet-penetration-remote-access.md)
- [写公众号不用再手动排版：飞牛 NAS 用一个容器装 doocs/md，Markdown 实时出公众号样式，配内网穿透在公司也能写](professional-wechat-markdown-editor-nas-simple-deployment-doocs-md-ai-intelligent-layout-beautiful-graphic-content.md)
- [自建备忘录 Memos 飞牛 NAS 极简部署，Docker 一把梭随手记，内网穿透在外面照样翻](memos-self-hosted-notes-nas-docker-deployment-intranet-penetration-remote-access.md)
- [轻量级Markdown笔记应用NoteFlow，本地部署无需数据库，支持内网穿透远程访问笔记管理系统](noteflow-lightweight-markdown-note-app-local-deployment-no-database-intranet-penetration-remote-access.md)
- [Note Mark 笔记：NAS Docker 部署 + 内网穿透](note-mark-nas-docker-markdown-notes-search-intranet-penetration.md)
