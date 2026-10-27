---
title: "开源持仓记录工具Ghostfolio私密部署指南，NAS一键Docker快速安装配置教程"
date: 2026-10-15
description: "Ghostfolio 是一款开源的个人持仓记录工具，记录股票、基金、ETF 的买入卖出，自动算收益、画净资产走势和资产占比。本文用飞牛 Docker Compose 部署，并用 PassNAT 开放到公网，包含编排、隧道创建、建账户和关闭用户注册的完整步骤。"
canonical: https://www.passnat.com/posts/ghostfolio-nas-docker-portfolio-tracker-passnat-deployment-guide
---

## 项目介绍

Ghostfolio 是一个开源的个人持仓记录工具，可以把股票、ETF、基金、加密货币的买入卖出一笔笔记下来，它自己算出每只标的的收益、整体净资产的走势，还能按资产类别、币种、地区把配置占比画成图。数据存在你自己的服务器上，不用把持仓交给任何第三方理财应用。行情默认从 Yahoo Finance、CoinGecko 这类公开数据源拉取，不需要额外申请密钥。

项目地址：[点我跳转](https://github.com/ghostfolio/ghostfolio)

## 部署安装

下面用飞牛 Docker Compose 部署 Ghostfolio，再用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，出门在手机上也能记一笔、看一眼。

### 一、在飞牛 Docker 新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 ghostfolio，路径在「我的文件」里挑一个空文件夹，没有就当场新建一个。来源选择创建 docker-compose.yml，把下面整段编排贴进去，勾上「创建项目后立即启动」，点确认。

```yaml
services:
  ghostfolio:
    image: ghostfolio/ghostfolio:latest
    ports:
      - "3498:3333"
    environment:
      ACCESS_TOKEN_SALT: change-me-salt
      JWT_SECRET_KEY: change-me-jwt
      DATABASE_URL: postgresql://user:change-me-pg@postgres:5432/ghostfolio-db?connect_timeout=300
      REDIS_HOST: redis
      REDIS_PORT: "6379"
      REDIS_PASSWORD: change-me-redis
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    restart: unless-stopped

  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: ghostfolio-db
      POSTGRES_USER: user
      POSTGRES_PASSWORD: change-me-pg
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -d ghostfolio-db -U user"]
      interval: 10s
      timeout: 5s
      retries: 5
    volumes:
      - ./pgdata:/var/lib/postgresql/data
    restart: unless-stopped

  redis:
    image: redis:alpine
    command: redis-server --requirepass change-me-redis
    healthcheck:
      test: ["CMD-SHELL", "redis-cli --pass change-me-redis ping | grep PONG"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped
```

几个认的地方：3498 是 NAS 对外的端口，映射到容器里的 3333，等下建隧道要填它；ACCESS\_TOKEN\_SALT、JWT\_SECRET\_KEY 这两个，以及 DATABASE\_URL 里的数据库口令、REDIS\_PASSWORD，都换成你自己的随机字符串，数据库口令在 postgres 服务里也要写成同一个，redis 的口令在 command 里同样要一致；./pgdata 是数据库的数据目录，备份就拷这个文件夹。

第一次启动会先跑数据库迁移，镜像要拉一会儿，启动也比较慢，等列表里 ghostfolio 变成正在运行、再过一两分钟再去访问。注意 DATABASE\_URL 保持上面这种写法，不要额外加 sslmode 参数。

### 二、创建隧道，开放到公网

飞牛的「应用中心」里搜 PassNAT，装上内网穿透插件，打开它，点「创建隧道」。隧道名称起 ghostfolio（12 个字符以内），接入节点自动分配，本地 IP 保持默认的 127.0.0.1，本地端口填 3498，提交。建好后点电源键，绿点亮了就是通了，列表里这一行会出现公网地址。

### 三、第一次访问：建账户

用公网地址打开，点「创建账户」，勾上同意，点继续。页面会给出一串安全令牌，它只显示这一次，点「复制到剪贴板」存好，之后登录靠它，丢了没办法找回。

第一个建出来的账户就是管理员，所以隧道开着之后，第一步先把它建了。

### 四、记一笔买入

进到页面右下角点「加号」添加活动，类型选买入，搜代码，选对标的，填日期、数量和买入时花的钱，点保存。记几笔之后回到概览，就能看到净资产、收益和走势线，持仓页按每只标的列出了数量和收益，下面还有按资产类别的占比。

### 五、关掉用户注册

账户建好之后，进「管理」页，把「用户注册」这个开关关掉。这样别人拿到你的公网地址也没法再注册，里面只有你自己。

### 备份与更新

数据都在 ./pgdata 这个文件夹里，把它整个拷走就是备份。要更新镜像，在飞牛里停掉项目，拉取新的 ghostfolio 镜像后再启动。

---

原文：<https://www.passnat.com/posts/ghostfolio-nas-docker-portfolio-tracker-passnat-deployment-guide>


## 相关文章

- [自建 changedetection.io：飞牛 NAS Docker 部署 + 内网穿透外网访问](changedetection-io-website-change-monitor-nas-docker-deployment-intranet-penetration.md)
- [Homebox 飞牛 NAS 部署与公网查物品档案](home-asset-management-system-nas-deployment.md)
- [Mealie 飞牛 NAS 部署与手机采购清单](mealie-private-recipe-library-nas-deployment-35-languages-remote-access.md)
- [Navidrome 飞牛 NAS 部署与公网听歌](navidrome-docker-private-music-server.md)
- [Grocy 飞牛 NAS 部署与公网查库存](grocy-self-hosted-nas-deployment-internal-network-penetration-remote-access-food-inventory.md)
