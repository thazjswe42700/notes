---
title: "AdventureLog 自建旅行地图：NAS Docker 部署 + 行程分享"
date: 2026-10-18
description: "开源自托管旅行日志：互动地图点亮去过的地方、行程按天排、链接发给家人。Compose 部署、首次启动要点、PassNAT 穿透、常见问题。"
canonical: https://www.passnat.com/posts/open-source-travel-log-adventurelog-docker-deploy-nas-personal-map
---

## 项目介绍

AdventureLog 是一个开源的旅行日志与行程规划应用：去过的地方在地图上打点，可以记日期、写笔记、标分类；一次旅行做成一个「收藏」（Collection），把地点按天排进去，就是行程表。

它最大的特点是「一张自己的世界地图」——首页会统计你走过几个国家、几个地区、几个城市，地图上按已访问和计划中两种状态显示地点。行程可以设为公开，生成分享图或者直接把链接发给家人。

技术上是一个 Django + PostGIS 的后端，前端打包在同一个应用镜像里，所以现在只需要一个应用容器加一个数据库容器。

项目地址：[点我跳转](https://github.com/seanmorley15/AdventureLog)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。两个容器，一份 Compose 编排就够了。

### 一、想在外面看，先把 3505 放出去

只在局域网里用，这一步可以跳过，直接看第二步。但如果你想把行程链接发给不在家的家人、或在外面用手机打开，就先在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **3505**，点开、按一下电源，状态变成运行中。详情里那个公网地址先记下来 —— 下一步要填进编排。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 adventurelog，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  app:
    image: ghcr.io/seanmorley15/adventurelog:latest
    container_name: adventurelog
    restart: unless-stopped
    environment:
      # 分享链接 / 公开页面用的地址，换成你外网访问的地址
      SITE_URL: http://nas.local:3505
      POSTGRES_DB: database
      POSTGRES_USER: adventure
      POSTGRES_PASSWORD: change-me-please
      PGHOST: db
    ports:
      - "3505:80"
    volumes:
      - adventurelog_media:/code/media/
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgis/postgis:16-3.5
    container_name: adventurelog-db
    restart: unless-stopped
    environment:
      POSTGRES_DB: database
      POSTGRES_USER: adventure
      POSTGRES_PASSWORD: change-me-please
    volumes:
      - postgres_data:/var/lib/postgresql/data/
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U adventure -d database"]
      interval: 5s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
  adventurelog_media:
```

两处要改：**SITE\_URL** 换成你实际打开这个应用的地址（如果做了第一步，就填隧道详情里的公网地址）；**POSTGRES\_PASSWORD** 换成你自己的口令，两个服务里要写成一样的。冒号前面那个 3505 是 NAS 上的端口，被占了就改它，后面的 80 不用动。

勾上「创建项目后立即启动」，点确认。数据库容器先起来，应用容器等它健康检查通过再启动。

### 三、第一次打开要等几分钟

浏览器打开 **http://NAS的局域网地址:3505**。**第一次启动会比平时慢很多**：应用要先建表，再把国家、地区、城市数据导入数据库，这一步在日志里显示为 download-countries，实测要五六分钟。这期间网页打不开是正常的，等日志里出现 gunicorn entered RUNNING 再刷新。

默认账号是 **admin**，密码也是 **admin**，登录后先到「个人资料」里把密码改掉。

进入后是首页，顶部一句「您已经探索了 N 个国家/地区」，下面是统计卡：访问过的国家、访问地点、收藏、访问过的地区、访问过的城市。

### 四、记下第一个地点

点左边的「地点」→「创建地点」。在「快速开始」里搜地名（比如 West Lake Hangzhou），从结果里点一个，点继续，进入「详细信息」：填名称、选类别（类别可以自己建，带个图标）、打个分、写点笔记；「访问次数」里加一段日期，保存。

也可以不用搜索：在下面的地图上点一下，坐标就填好了。地图底图是 OpenStreetMap，NAS 和外网通才出图。

地点建好后，首页的地图和统计卡会跟着变；「地点」列表里能看到每个地点的类别、评分和访问次数。

### 五、做一条行程，发给家人

点「收藏」→ 新建收藏，填名字、起止日期，把这次要去的地点按天加进去（就是行程表）。

收藏可以设为公开：打开收藏详情，点「对外分享」，可以下载方形、故事、景观三种尺寸的分享图，也可以复制链接。链接里的域名就是编排里填的 SITE\_URL —— 所以想让家人在外网打开，SITE\_URL 必须填公网地址。

家人打开链接不用登录就能看这条行程；想一起编辑，就在「用户」里邀请账号。

### 六、在外面用手机打开

手机浏览器直接打开隧道详情里的那个公网地址，用同一个账号登录：首页、地图、地点列表和电脑上一样，地图上的点可以放大看，行程按天排。走到一个新地方，当场加一条，回家打开电脑就是完整的记录。

### 几点说明

**数据都在这两个卷里。** postgres\_data 是数据库，adventurelog\_media 是上传的图片。备份这两个卷就够了，升级镜像不会丢数据。

**改 SITE\_URL 要重建容器。** 它是在启动时读进环境变量的，改完 compose 重新 up 一次才生效；改之前生成的分享链接还是旧地址。

**地图要能上外网。** 底图走 MapLibre + CARTO 的在线瓦片，NAS 完全没有外网时地图会是空白，但地点和统计照常记录。

**端口冲突。** 宿主 3505 被占了就改编排里冒号前面那个数字，记得隧道端口跟着改。

**更新。** 拉新镜像重建容器即可。数据库在卷里，重建不会清空；但大版本升级前还是先备份一下卷。

---

原文：<https://www.passnat.com/posts/open-source-travel-log-adventurelog-docker-deploy-nas-personal-map>
