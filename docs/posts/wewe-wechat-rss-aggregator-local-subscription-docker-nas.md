---
title: "微信公众号RSS聚合工具WeWe，本地化订阅管理，Docker一键部署NAS轻松搞定"
date: 2025-10-16
description: "WeWe RSS是优雅的微信公众号订阅聚合工具，支持RSS生成、全文输出、自动定时更新，可导出OPML文件。基于微信读书接口开发，支持多种RSS格式输出。提供Docker Compose和SQLite两种部署方式，开箱即用，所有数据本地存储，完全由你掌控。"
canonical: https://www.passnat.com/posts/wewe-wechat-rss-aggregator-local-subscription-docker-nas
---

WeWe RSS - 优雅的微信公众号订阅方案

## 项目介绍

WeWe RSS 是一个开箱即用的微信公众号订阅聚合工具，让你用更优雅的方式订阅和管理微信公众号。基于微信读书接口开发，支持自动定时更新、RSS生成、全文输出等功能，让公众号阅读体验焕然一新。

项目具有以下亮点：支持多种RSS格式输出（Atom、RSS、JSON），可以导出OPML文件便于迁移，支持标题过滤和手动更新，后台自动定时更新内容，所有内容完全本地化存储。无需复杂配置，一键启动即可使用。

项目地址： [点我跳转](https://github.com/cooderl/wewe-rss)

## 部署安装

如果你想在公网上访问这个项目，建议配置内网穿透工具来实现远程访问。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手指南。

### Docker Compose 部署（推荐）

这是最简便的部署方式，适合大多数用户。

复制下列的配置文件到你的NAS或服务器中，保存为 `docker-compose.yml`：

```yaml
version: '3.9'

services:
  db:
    image: mysql:8.3.0
    command: --mysql-native-password=ON
    environment:
      # 请修改为自己的密码
      MYSQL_ROOT_PASSWORD: 123456
      TZ: 'Asia/Shanghai'
      MYSQL_DATABASE: 'wewe-rss'
    volumes:
      - db_data:/var/lib/mysql
    healthcheck:
      test: ['CMD', 'mysqladmin', 'ping', '-h', 'localhost']
      timeout: 45s
      interval: 10s
      retries: 10

  app:
    image: cooderl/wewe-rss:latest
    ports:
      - 4000:4000
    depends_on:
      db:
        condition: service_healthy
    environment:
      # 数据库连接地址
      - DATABASE_URL=mysql://root:123456@db:3306/wewe-rss?schema=public&connect_timeout=30&pool_timeout=30&socket_timeout=30
      # 服务接口请求授权码
      - AUTH_CODE=123567
      # 提取全文内容模式
      # - FEED_MODE=fulltext
      # 定时更新订阅源Cron表达式
      # - CRON_EXPRESSION=35 5,17 * * *
      # 服务接口请求限制，每分钟请求次数
      # - MAX_REQUEST_PER_MINUTE=60
      # 外网访问时，需设置为服务器的公网 IP 或者域名地址
      # - SERVER_ORIGIN_URL=http://localhost:4000

volumes:
  db_data:
```

打开你的飞牛NAS、群晖NAS或其他支持Docker的NAS设备，找到Docker应用，选择上传或导入Compose文件。

**关键配置说明：**

这里需要修改的是数据库密码。将配置文件中的 `MYSQL\_ROOT\_PASSWORD` 和 `DATABASE\_URL` 中的 `123456` 改成你自己的安全密码，`AUTH\_CODE` 改成你自己的授权码（用于API请求验证）。其他配置保持不变即可。

如果提示端口被占用，可以修改 `ports` 字段中的第一个端口号（比如改成 `4001:4000`），但容器内部端口 `4000` 需要保持不变。

**环境变量设置说明：**

必须设置：

`DATABASE\_URL`：数据库连接地址，按照模板格式填写

`AUTH\_CODE`：API请求的授权码，自定义安全字符串

可选设置（根据需求）：

`FEED\_MODE`：设置为 `fulltext` 可输出完整文章内容（会增加响应时间和内存占用）

`CRON\_EXPRESSION`：定时更新表达式，默认为 `35 5,17 \* \* \*`（每天5点35分和17点35分更新）

`MAX\_REQUEST\_PER\_MINUTE`：每分钟最大请求次数，默认60

`SERVER\_ORIGIN\_URL`：外网访问时设置为你的服务器公网IP或域名

其他变量无需设置，保持默认即可。

启动容器后，等待MySQL数据库初始化完成（通常需要30秒左右），然后就可以访问了。

### Docker 单命令部署

如果你只想快速体验，也可以使用SQLite版本的单命令启动：

```bash
docker run -d \
  --name wewe-rss \
  -p 4000:4000 \
  -e DATABASE_TYPE=sqlite \
  -e AUTH_CODE=123567 \
  -v $(pwd)/data:/app/data \
  cooderl/wewe-rss-sqlite:latest
```

打开你的NAS设备，进入Docker管理界面，选择'本地镜像'或'添加镜像'，从URL添加镜像。

镜像地址填写：`cooderl/wewe-rss-sqlite:latest`

等待镜像下载完成后，点击启动按钮启动容器。

**端口配置：**

将容器端口 `4000` 映射到你的NAS上，如果NAS的4000端口已被占用，可以改为其他端口（如 `4001`），但容器内部端口必须是 `4000`。

**存储配置：**

创建一个文件夹用于存储数据库文件（如 `/data`），将其挂载到容器的 `/app/data` 目录，这样即使容器重启数据也不会丢失。

**环境变量：**

`DATABASE\_TYPE`：填写 `sqlite`

`AUTH\_CODE`：设置你的授权码

其他不需要设置

启动成功后可以访问 `http://你的NAS地址:4000` 进入WeWe RSS的管理界面。

## 使用方式

1. 添加账号：进入账号管理页面，点击'添加账号'，使用微信扫码登录微信读书账号。登录时注意不要勾选'24小时后自动退出'选项。

2. 订阅公众号：进入公众号源管理，点击'添加'，粘贴微信公众号的分享链接，系统会自动识别并添加订阅。

3. 生成RSS：订阅成功后，系统会自动生成对应的RSS源链接，你可以在任何RSS阅读器中使用。

## 部署完成

到这里WeWe RSS就已经部署完毕了，你现在可以尽情享受更优雅的微信公众号阅读体验。所有的订阅数据都存储在本地，完全由你掌控。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/wewe-wechat-rss-aggregator-local-subscription-docker-nas>


## 相关文章

- [自建 RSS 阅读器 FreshRSS：飞牛 NAS Docker 部署，关注的更新汇到一页，配内网穿透在外面也能刷](self-hosted-rss-reader-freshrss-nas-docker-deployment-intranet-penetration.md)
- [使用Morss完整抓取RSS全文内容，NAS Docker极速部署，内网穿透远程订阅](morss-nas-docker-rss-remote-subscription.md)
- [自建 RSS 源 RSSHub：飞牛 NAS Docker 部署，给没有 RSS 的网站造一个订阅源，配内网穿透在外面也能刷](nas-rss-aggregation-service-docker-rsshub-intranet-penetration-remote-subscription.md)
- [开源RSS处理管道系统，灵活转换聚合内容，NAS Docker快速部署，内网穿透远程访问](open-source-rss-processing-pipeline-flexible-content-aggregation-nas-docker-deployment-intranet-penetration.md)
- [自建RSS阅读聚合中心，内网穿透远程订阅，Selfoss极简Docker部署完全指南](self-hosted-rss-reader-selfoss-docker-deployment-guide.md)
