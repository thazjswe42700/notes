---
title: "Dawarich 飞牛 NAS 部署：自己的位置轨迹自己存"
date: 2026-10-14
description: "手机手表的轨迹丢进自己 NAS，地图回看走过的路：Dawarich 完整编排、公网地址怎么填、GPX 导入和出门访问，含飞牛 PassNAT 插件隧道步骤。"
canonical: https://www.passnat.com/posts/dawarich-nas-location-history-self-hosted-internal-penetration
---

## 项目介绍

Dawarich 是一个开源的位置轨迹库，可以看作 Google 时间线的自托管替代：手机、手表导出的轨迹丢进来，地图上就能回看走过的路，按年、按月统计走过多少公里、去过哪些城市。数据全存在自己家里，不交给任何地图软件。

项目源码：[Freika/dawarich](https://github.com/Freika/dawarich)

官方文档：[Dawarich Docs](https://dawarich.app/docs/)

## 部署安装

下面用飞牛 Docker Compose 部署，再通过 [帕斯内网穿透](https://passnat.com) 把它开放到公网，出门在外也能翻。

### 一、先把公网地址拿到手

Dawarich 有个特别的地方：编排里的 **APPLICATION\_HOSTS** 必须填你实际访问它的地址。填错不是打不开页面，而是页面能看、表单提交全被拒——所以先把隧道建好，拿到地址再部署。

飞牛应用中心搜 PassNAT，装上内网穿透插件并打开；创建隧道，类型选 HTTP，域名自己起一个，本地地址留默认，端口填 3496（跟下面编排里对上）；按电源通电，把公网地址记下来。

### 二、在飞牛 Docker 新建 Compose 项目

打开 Docker，切到 Compose，点新增项目。项目名称填 dawarich；路径挑一个空文件夹（我在「我的文件」下建了 mytrail-demo，里面建 dawarich-app）；来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  dawarich_redis:
    image: redis:7.4-alpine
    command: redis-server --save 900 1 --save 300 10 --appendonly no
    volumes:
      - dawarich_shared:/data
    restart: always

  dawarich_db:
    image: postgis/postgis:17-3.5-alpine
    shm_size: 1G
    volumes:
      - dawarich_db_data:/var/lib/postgresql/data
      - dawarich_shared:/var/shared
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: ChangeMe_DbPass
      POSTGRES_DB: dawarich_development
    restart: always

  dawarich_app:
    image: freikin/dawarich:latest
    volumes:
      - dawarich_public:/var/app/public
      - dawarich_watched:/var/app/tmp/imports/watched
      - dawarich_storage:/var/app/storage
      - dawarich_db_data:/dawarich_db_data
    ports:
      - "3496:3000"
    stdin_open: true
    tty: true
    entrypoint: web-entrypoint.sh
    command: ['bin/rails', 'server', '-p', '3000', '-b', '::']
    restart: unless-stopped
    environment:
      RAILS_ENV: production
      REDIS_URL: redis://dawarich_redis:6379
      DATABASE_HOST: dawarich_db
      DATABASE_PORT: 5432
      DATABASE_USERNAME: postgres
      DATABASE_PASSWORD: ChangeMe_DbPass
      DATABASE_NAME: dawarich_development
      APPLICATION_HOSTS: localhost,你的公网地址
      TIME_ZONE: Asia/Shanghai
      APPLICATION_PROTOCOL: http
      SECRET_KEY_BASE: 换成你自己的长随机串
      RAILS_LOG_TO_STDOUT: "true"
      SELF_HOSTED: "true"
      STORE_GEODATA: "true"
    depends_on:
      - dawarich_db
      - dawarich_redis

  dawarich_sidekiq:
    image: freikin/dawarich:latest
    volumes:
      - dawarich_public:/var/app/public
      - dawarich_watched:/var/app/tmp/imports/watched
      - dawarich_storage:/var/app/storage
    stdin_open: true
    tty: true
    entrypoint: sidekiq-entrypoint.sh
    command: ['sidekiq']
    restart: unless-stopped
    environment:
      RAILS_ENV: production
      REDIS_URL: redis://dawarich_redis:6379
      DATABASE_HOST: dawarich_db
      DATABASE_PORT: 5432
      DATABASE_USERNAME: postgres
      DATABASE_PASSWORD: ChangeMe_DbPass
      DATABASE_NAME: dawarich_development
      APPLICATION_HOSTS: localhost,你的公网地址
      APPLICATION_PROTOCOL: http
      SECRET_KEY_BASE: 换成你自己的长随机串
      RAILS_LOG_TO_STDOUT: "true"
      SELF_HOSTED: "true"
      STORE_GEODATA: "true"
    depends_on:
      - dawarich_db
      - dawarich_redis
      - dawarich_app

volumes:
  dawarich_db_data:
  dawarich_shared:
  dawarich_public:
  dawarich_watched:
  dawarich_storage:
```

几处要改成你自己的：口令三处（数据库的 **POSTGRES\_PASSWORD** 和两处 **DATABASE\_PASSWORD**）要填成一样的值；**SECRET\_KEY\_BASE** 换成一串长随机串。

最关键的是 **APPLICATION\_HOSTS**：填你刚才拿到的公网地址（只写主机名，不要带 http://），**localhost** 也留着。这一步填错的症状就是上面说的「页面能开、提交全被拒」，很多人会以为是权限问题，其实是主机校验。

3496 是 NAS 对外的网页端口，容器内是 3000。数据全在具名卷里（dawarich\_db\_data 等五个），升级迁移都跟着 Docker 卷走。

勾上「创建项目后立即启动」，点确认，等镜像拉完、四个容器都跑起来。第一次启动要跑数据库迁移，多等一会儿。

## 初始化

浏览器打开公网地址。它自带一个默认账号（README 里的 Default Credentials：demo@dawarich.app / safepassword），登录后第一件事：右上角进 Account settings，把邮箱和口令换成你自己的。隧道一开，别人也可能碰到这个默认口令，这一步别拖。

## 把轨迹导进来

My data → Imports → New import。支持的格式很多：Google 时间线导出的 Records.json、GPX、GeoJSON、OwnTracks、KML、FIT、TCX、CSV 都行，手表手机导出的 GPX 直接丢进来。

导入是后台任务，状态变 Completed 就进库了，地图上立刻多出走过的轨迹。

## 看图

Map 页可以同时打开轨迹、热力图、时间线：哪天走了哪条线、走了多远都标着；热力图一眼看出常去的地方。Stats 页按年月统计总里程、去过多少国家多少城市。同一个地址在任何设备的浏览器里都打得开，出门在外照样翻。

## 让它自己记（可选）

不想手动导入也没关系：Dawarich 有官方 iOS / Android 客户端，也支持 OwnTracks、Overland、GPSLogger、PhoneTrack、Home Assistant 这些上报端。把上报地址指到你的公网地址，位置就会定时发回自己家的 NAS。

---

原文：<https://www.passnat.com/posts/dawarich-nas-location-history-self-hosted-internal-penetration>


## 相关文章

- [workout-tracker 飞牛 NAS 部署：GPX 轨迹库与运动统计](workout-tracker-nas-gpx-workout-log.md)
- [Navidrome 飞牛 NAS 部署与公网听歌](navidrome-docker-private-music-server.md)
- [Mealie 飞牛 NAS 部署与手机采购清单](mealie-private-recipe-library-nas-deployment-35-languages-remote-access.md)
- [Grocy 飞牛 NAS 部署与公网查库存](grocy-self-hosted-nas-deployment-internal-network-penetration-remote-access-food-inventory.md)
- [Homebox 飞牛 NAS 部署与公网查物品档案](home-asset-management-system-nas-deployment.md)
