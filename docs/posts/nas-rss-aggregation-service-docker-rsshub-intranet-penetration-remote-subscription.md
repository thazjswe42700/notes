---
title: "自建 RSS 源 RSSHub：飞牛 NAS Docker 部署，给没有 RSS 的网站造一个订阅源，配内网穿透在外面也能刷"
date: 2025-10-16
description: "在飞牛 NAS 上用 Docker 部署 RSSHub 自建 RSS 源，给没有提供订阅的网站造一个 RSS，单容器不用 Redis，附可以直接抄的订阅地址和常用参数，并通过内网穿透在外网也能随时刷。"
canonical: https://www.passnat.com/posts/nas-rss-aggregation-service-docker-rsshub-intranet-penetration-remote-subscription
---

## 项目介绍

RSSHub 干的就一件事：网站自己不提供 RSS，它替你造一个。很多站点这些年把订阅入口去掉了，想追更新只能自己一个一个点进去看；RSSHub 把这些站点的内容抓回来，按标准 RSS 的格式吐出去，你拿任何一个 RSS 阅读器订上就行，不用管它是从哪儿来的。

它跟大多数 NAS 应用不太一样的地方是：它没有账号，也没有后台。你不用登录，也不用在界面上添加订阅源 —— 它的全部用法就是拼一个网址出来，网址里带着「要哪个站、哪个板块」，打开就是一份 RSS。这一点后面会专门写一节，因为不知道的话，装完打开会一头雾水。

自己装一份的好处也在这儿：公共实例人多的时候容易被上游限流，而且它知道你在订什么；装在自己的 NAS 上，抓取是从你家里的网出去的，订阅列表也只有你自己看得见。它本身很轻，一个容器就够，官方镜像一百二十兆出头，实测跑起来占用不到三百兆内存。

项目地址：[DIYgod/RSSHub](https://github.com/DIYgod/RSSHub)

## 部署安装

装在自己的 NAS 上之后，它默认只有家里的网络能打开。可 RSS 这东西恰恰是在外面才用得上 —— 通勤路上、午休的时候刷两眼，只能在家看的订阅等于没订。家里没有公网 IP 的话，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里就能直接装到插件，不用另外找工具。具体的操作步骤可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)。下面这套流程在飞牛 NAS 上从头到尾走一遍。

### 第一步：准备编排文件

下面这份编排可以直接用，只有一处需要按自己的情况改：冒号左边的宿主端口。

```yaml
services:
  rsshub:
    image: diygod/rsshub:latest
    container_name: rsshub
    restart: unless-stopped
    ports:
      # 左边 3403 是 NAS 上对外的端口，浏览器敲它；右边 1200 是容器里的
      - "3403:1200"
    environment:
      # 缓存放内存里，就不用再起一个 Redis（这也是默认值）
      CACHE_TYPE: memory
      # 抓回来的内容缓存十分钟，别一刷新就去打人家网站
      CACHE_EXPIRE: 600
      # 不填的话文章时间和日志都是 UTC，差八小时
      TZ: Asia/Shanghai
```

端口：冒号左边的 3403 是待会儿在浏览器里输的宿主端口，也是建隧道时要填的那个；右边的 1200 是容器内部的端口，不要动。3403 如果被别的容器占了，换一个没用过的即可。

缓存：CACHE\_TYPE 填 memory，缓存直接放在容器的内存里。官方的示例编排里还带了一个 Redis 容器和一个 Browserless 浏览器容器，家用场景这两个都可以不要 —— CACHE\_TYPE 的默认值本来就是 memory，多起一个 Redis 只是为了缓存；Browserless 是给少数需要浏览器渲染才抓得到的站点用的，本文后面给的地址一条都用不上它。少两个容器，内存和维护都省下来了。

CACHE\_EXPIRE 是抓回来的内容缓存多久，单位是秒，600 就是十分钟。这个值不建议调得太小：你每刷新一次它就要去打人家网站一次，间隔太密容易被对方限流。

时区：TZ 填 Asia/Shanghai。这一行不填的话，文章上显示的时间和容器日志都是 UTC，跟本地差八小时。

这份编排里没有挂载任何目录，因为它不存数据 —— 内容是每次现抓的，缓存在内存里，容器重启就重新抓一遍。所以也没有什么需要备份的东西。

镜像标签跟着官方走用 latest 就行，不建议自己钉一个具体的版本号 —— 钉死之后上游更新了你这边还停在旧版。

### 第二步：在飞牛上建 Compose 项目

打开飞牛的 Docker 应用，切到 Compose，点右上角的新增项目。项目名称填 rsshub。

路径这一栏点开，在「我的文件」下面挑一个空文件夹选中 —— 这个文件夹以后就是这个项目的家。

来源选「创建 docker-compose.yml」，把上面那份编排整段贴进去。

下面「创建项目后立即启动」勾上，点创建。镜像一百二十兆出头，拉完二十秒上下就跑起来了。

想确认它真的起来了，在家里的网络上打开 http://你的NAS地址:3403 —— 看到一张写着 Welcome to RSSHub! 的页面就是好的。注意这张页面就是它的全部界面了，下一节会说清楚为什么。

### 第三步：用内网穿透开放到公网

在飞牛的应用中心里搜 PassNAT，装上，账号登一次。打开插件点创建隧道：名字随便起；协议选 TCP；本地 IP 这一栏留空就好，默认就是 127.0.0.1，不用去查 NAS 的局域网地址；本地端口填上面那个 3403。

建好之后按一下电源，等状态转成运行中，面板上那个「IP:端口」就是在外面要用的公网地址。本文后面所有的网址都写成「你的NAS地址:3403」的形式，那是在家里的网络上用的；出门在外的时候，把这一段换成 PassNAT 给你的那个公网地址，后面的路由部分一个字都不用改。

顺带说一句：RSSHub 自己没有账号体系，任何人拿到这个地址都能用。如果你不希望它一直挂在公网上，平时把隧道的电源关掉就行，家里的网络照常访问，不用动容器。

## 怎么用

### 先说清楚它长什么样：没有后台，也没有订阅源列表

打开之后，你会看到一张静态的欢迎页：一个 logo，加上 Welcome to RSSHub! 这么几行字。这就是它的全部界面 —— 没有登录框，没有设置页，也没有一个可以浏览「它支持哪些网站」的列表。实际去访问 /routes 或者 /api/routes 都是 404，这不是你装漏了什么，它本来就没有这些页面。

所以别在界面上找入口，它的用法是拼网址：地址后面接一段路由，就是一份 RSS。「有哪些路由可以用」查官方文档，文档里每一条都写着路径长什么样、要填哪些参数。

路由文档：[docs.rsshub.app](https://docs.rsshub.app/routes/popular)

### 几条可以直接抄的地址

下面这几条是在自建实例上实际跑通过的。把「你的NAS地址」换成你 NAS 的局域网地址，浏览器打开能看到一份 XML，就说明通了；把同一个网址粘进任何一个 RSS 阅读器，就订上了。

```
http://你的NAS地址:3403/dockerhub/tag/library/mariadb     盯 MariaDB 官方镜像出新 tag
http://你的NAS地址:3403/dockerhub/tag/library/postgres    盯 PostgreSQL 官方镜像出新 tag
http://你的NAS地址:3403/github/issue/DIYgod/RSSHub        盯一个 GitHub 仓库的 issue
http://你的NAS地址:3403/ithome/it                         IT 之家
http://你的NAS地址:3403/sspai/index                       少数派
http://你的NAS地址:3403/36kr/newsflashes                  36 氪快讯
http://你的NAS地址:3403/zhihu/daily                       知乎日报
```

第一条和第二条尤其适合 NAS 玩家：Docker Hub 本身不提供 RSS，而「我常用的这个镜像出没出新版本」又是每天都想知道的事。把 library/mariadb 换成你关心的任何一个镜像名就行，别人仓库下的镜像写成 作者名/镜像名 也一样能用。

有一件事最好先知道：路由的可用性是会变的。上面这几条是写这篇文章的时候一条一条试过的，但上游站点改版、加风控，都会让某一条暂时失效；还有一些路由需要额外填登录凭据才能用。我当时另外试的三条就分别返回了 503 和 404，而它们在文档里看不出任何区别。所以某一条打不开的时候，先换一条试试 —— 换一条能通，就说明服务本身是好的，问题在那一条路由上，不是你装错了。

### 几个把订阅调顺手的参数

所有路由都支持一组通用参数，直接加在网址后面，第一个用问号、后面用 & 连接。下面这三个是最常用的：

```
http://你的NAS地址:3403/dockerhub/tag/library/mariadb?limit=3
http://你的NAS地址:3403/dockerhub/tag/library/mariadb?filterout=rc
http://你的NAS地址:3403/dockerhub/tag/library/mariadb?format=json
```

limit 是只要前几条。刚订上的时候一次灌进来几十条很容易劝退，先限成三条看看效果。

filterout 是把标题里含某个词的条目扔掉，这个最实用。还是拿盯镜像举例：官方镜像的 tag 里有大量的预览版（名字里带 rc），而你多半只关心正式版 —— 加上 filterout=rc 之后预览版就都滤掉了。反过来 filter=rc 是只留下含这个词的，两个正好相反。

format 是换输出格式，默认是 RSS，写成 format=json 会给你一份 JSON Feed，写成 format=atom 是 Atom，接自己的脚本时用得上。

## 一些使用上的细节

它是现抓的，不是攒下来的。你打开一条地址的时候它才去访问上游，所以第一次打开慢一点很正常；十分钟之内再打开同一条，走的是缓存，会快很多。也正因为如此，它不需要挂载数据目录，容器删了重建也不会丢东西。

访问速度取决于上游站点。国内的站点通常一两秒之内就回来了，需要从境外抓的可能要等几秒，这跟你 NAS 的网络环境有关，不是它慢。

少数站点需要浏览器渲染才抓得到内容，这类路由要用官方另一个带浏览器的镜像（标签是 chromium-bundled），体积和内存占用都会明显上去。建议先用本文这份轻量的编排跑着，真的遇到抓不下来的站点再换。

一份订阅只要地址不变就一直有效，所以隧道的公网地址最好别换来换去，换了之前订过的那些地址就都失效了，要在阅读器里挨个改。

最后提一句用法上的思路：RSSHub 出来的是标准 RSS，它不负责「读」。你可以把地址丢进手机上的阅读器 App，也可以丢进自己另外装的一个 RSS 阅读器，甚至直接让脚本去解析它 —— 到这一步它的活就干完了。

---

原文：<https://www.passnat.com/posts/nas-rss-aggregation-service-docker-rsshub-intranet-penetration-remote-subscription>


## 相关文章

- [自建 RSS 阅读器 FreshRSS：飞牛 NAS Docker 部署，关注的更新汇到一页，配内网穿透在外面也能刷](self-hosted-rss-reader-freshrss-nas-docker-deployment-intranet-penetration.md)
- [使用Morss完整抓取RSS全文内容，NAS Docker极速部署，内网穿透远程订阅](morss-nas-docker-rss-remote-subscription.md)
- [自建RSS阅读聚合中心，内网穿透远程订阅，Selfoss极简Docker部署完全指南](self-hosted-rss-reader-selfoss-docker-deployment-guide.md)
- [开源RSS处理管道系统，灵活转换聚合内容，NAS Docker快速部署，内网穿透远程访问](open-source-rss-processing-pipeline-flexible-content-aggregation-nas-docker-deployment-intranet-penetration.md)
- [轻量级自托管仪表板Glance，极简部署NAS内网穿透远程访问信息聚合平台](glance-lightweight-self-hosted-dashboard-nas-intranet-penetration-remote-access-information-aggregation-platform.md)
