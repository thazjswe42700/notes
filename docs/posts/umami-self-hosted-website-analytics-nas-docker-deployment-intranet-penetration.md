---
title: "自建 Umami 网站统计：飞牛 NAS Docker 部署 + 内网穿透"
date: 2026-09-07
description: "网站访客数据不想交给第三方？在飞牛 NAS 上用 Docker Compose 自建 Umami，再用内网穿透开放到公网，让统计脚本在外网也加载得到。含完整编排文件和踩坑说明。"
canonical: https://www.passnat.com/posts/umami-self-hosted-website-analytics-nas-docker-deployment-intranet-penetration
---

## 项目介绍

Umami 是一个开源的网站统计工具，自己装在自己的服务器上。谁来过、看了哪几页、从哪个链接点进来的、用什么设备，它都替你记着。

跟常见的第三方统计比，它的两个特点是：数据存在你自己的库里，不经过别人；以及默认不写 cookie，靠匿名指纹算访客数，所以不用弹「本站使用 Cookie」那种横幅。

界面出厂是英文的，装完在右上角头像进「设置 → 偏好 → 语言」里选「中文」就行，这个选择存在浏览器本地，换个浏览器要再选一次。

项目地址：[点我跳转](https://github.com/umami-software/umami)

## 部署安装

Umami 装在内网之后有个绕不开的问题：它给你的那段统计脚本，地址是内网的。你把脚本贴到自己网站上，外面的访客根本加载不到它，也就收不到任何数据。所以这个工具跟别的自建服务不一样 —— 它必须能从公网访问才有意义。

我家是移动宽带，没有公网 IP，拿公网 IP 也没那么简单。想省事可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，不需要公网 IP，也不用改路由器。

### 第一步：准备编排文件

Umami 需要一个 PostgreSQL 数据库，下面这份编排把两个容器一起写好了，可以直接用。

```yaml
services:
  umami:
    image: ghcr.io/umami-software/umami:latest
    container_name: umami
    ports:
      # 左边 3100 是 NAS 上对外的端口，浏览器敲它；右边 3000 是容器里的
      - '3100:3000'
    environment:
      # 连下面那个 db，用户名密码库名跟 db 那三行对上就行
      DATABASE_URL: postgresql://umami:umami@db:5432/umami
      # 下面两串自己随便生成，别照抄我的
      APP_SECRET: replace-me-with-a-random-string
      TWO_FACTOR_ENCRYPTION_KEY: replace-me-with-a-64-character-hex-string
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped
  db:
    image: postgres:15-alpine
    container_name: umami-db
    environment:
      POSTGRES_DB: umami
      POSTGRES_USER: umami
      POSTGRES_PASSWORD: umami
    volumes:
      # 统计数据存这儿，删容器不丢
      - umami-db-data:/var/lib/postgresql/data
    healthcheck:
      test: ['CMD-SHELL', 'pg_isready -U umami']
      interval: 5s
      retries: 10
    restart: unless-stopped
volumes:
  umami-db-data:
```

镜像 tag 照着上游官方的 docker-compose.yml 写就行，用 latest，这样你什么时候照抄，装的都是当下的版本。这里有个坑值得单独说：带 postgresql- 前缀的 tag（比如 postgresql-latest）是 2.x 那条老线，只更新到 v2.16 就停了；3.x 把前缀去掉了，浮动 tag 就叫 latest。照着老教程写 postgresql-latest，装到的是旧大版本。

需要你自己改的有三处：

端口那一行是 '3100:3000'。左边的 3100 是 NAS 上对外的端口，一会儿浏览器里敲的就是它；右边的 3000 是容器内部的端口，不要改。3100 要是在你机器上被占了，改左边那个数字。

APP\_SECRET 和 TWO\_FACTOR\_ENCRYPTION\_KEY 这两串自己随便生成，别照抄。前者随便一串随机字符就行，后者要求是 64 位的十六进制（openssl rand -hex 32 能直接生成一串）。TWO\_FACTOR\_ENCRYPTION\_KEY 是 3.x 才加的，2.x 的老教程里没有，漏了容器起不来。

最下面那个 db 是数据库，跟着一块儿起来，不用你另外装。它的数据放在一个命名卷里，删掉容器重建也不会丢。

### 第二步：在飞牛里建 Compose 项目

打开飞牛桌面上的 Docker，切到上面的「Compose」标签，点「新增项目」。

项目名称填 umami。路径这里点右边的图标挑一个空文件夹，没有就在选择器里当场新建一个。

「来源」选「创建 docker-compose.yml」，把上面那段编排贴进编辑框，最后勾上「创建项目后立即启动」，点确认。

第一次启动比别的服务慢一些：要拉两个镜像，起来之后还要等数据库就绪、再自动建一遍表，整个过程一分多钟。这中间用浏览器打开会看到报错页，属于正常现象，等它建完就好了。

### 第三步：建隧道开放到公网

飞牛的应用中心里直接搜 PassNAT 就能装到客户端，装好之后账号登录一次即可。

打开插件，进「隧道」，点创建隧道。名字自己起一个，接入节点和传输协议都留默认。

「本地IP」这一栏不用填 —— 留空默认就是 127.0.0.1，插件本身跑在 NAS 上，回环地址指向的就是这台机器。本地端口填 3100，跟编排里左边那个数字保持一致。

建好之后在隧道列表里点开详情，按一下电源图标连接，端口列表里就能看到公网地址，形如「节点IP:端口」。

### 第四步：改掉出厂口令

这一步别跳过。Umami 的出厂账号是 admin，口令也是固定的一串，写在镜像里，谁都知道。一旦开放到公网，不改口令等于把后台交出去。

用刚才那个公网地址打开，登录进去，右上角头像进「设置 → 账户 → 个人资料」，点「修改密码」改掉。

### 第五步：添加网站，拿统计脚本

进「网站」，点「添加网站」，填一个名字和你要统计的域名，保存。

然后进这个网站的设置页，里面有一段「跟踪代码」，长这样：

```
<script defer src="http://你的公网地址/script.js"
        data-website-id="添加网站之后它给你的那串 ID"></script>
```

把这段贴到你自己网站的 head 里就行。这里是全篇最关键的一点：脚本里那个地址是 Umami 按「你当前用什么地址访问它」自动生成的。所以你要在**公网地址**下打开 Umami 再来复制这段代码 —— 在内网地址下复制出来的是内网地址，贴到网站上外面的人加载不到，数据永远是零。

贴好之后正常访问几次你的网站，回到 Umami 的面板，数据就出来了：今天多少访客、看了多少页、从哪个来源进来的、用什么设备。左边还有实时、留存、访问路径这些更细的视图。

## 常见问题

面板一直是零：先确认统计脚本里的地址是公网地址而不是内网地址，再确认「添加网站」时填的域名跟你实际访问的域名一致。

容器起来就退出：多半是 TWO\_FACTOR\_ENCRYPTION\_KEY 没填或者格式不对，它要求 64 位十六进制。

换了端口之后打不开：编排里 ports 左边的数字、隧道里的「本地端口」这两处要一致，改一个就得改另一个。

---

原文：<https://www.passnat.com/posts/umami-self-hosted-website-analytics-nas-docker-deployment-intranet-penetration>


## 相关文章

- [Homarr 自建服务导航页：飞牛 NAS Docker 部署 + 内网穿透外网访问](homarr-self-hosted-dashboard-nas-docker-deployment-intranet-penetration.md)
- [自建 Smart Mermaid：飞牛 NAS Docker 部署 + 内网穿透外网访问](smart-mermaid-ai-text-to-diagram-nas-docker-deployment-intranet-penetration.md)
- [自托管监控工具Uptime Kuma极简部署指南，NAS一键启动Docker容器实时监控服务可用性](uptime-kuma-nas-docker-deployment-guide.md)
- [本地部署AI大模型Ollama，离线运行Gemma Llama DeepSeek，支持内网穿透远程访问](local-deploy-ai-ollama-offline-gemma-llama-deepseek-intranet-tunneling.md)
- [自建 Netdata 实时监控：飞牛 NAS Docker 部署 + 内网穿透](netdata-realtime-server-monitoring-nas-docker-deployment-intranet-penetration.md)
