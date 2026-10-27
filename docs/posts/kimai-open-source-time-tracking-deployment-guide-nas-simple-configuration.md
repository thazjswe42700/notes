---
title: "开源时间追踪Kimai完整部署指南,NAS极简配置轻松打造专业计时系统"
date: 2025-10-16
description: "Kimai是专业开源时间追踪应用，支持自由职业者和企业用户。提供JSON API、发票生成、多计时器、多语言、双因素认证、权限管理、预算跟踪和详细报表。Docker快速部署，支持30+语言和多时区，帮助您精确记录项目耗时、提高工作效率和账单准确性。"
canonical: https://www.passnat.com/posts/kimai-open-source-time-tracking-deployment-guide-nas-simple-configuration
---

## 项目介绍

Kimai 是一个开源的工时记录工具，为自由职业者和接私活的开发者设计。哪个客户、哪个项目、干了多久，它都记到分钟；月底按项目筛选，能直接导出账单发给客户，不用再靠自己回忆。

它是网页版界面，手机浏览器也能打开。把它装在家里的 NAS 上，几年的工时数据都存在自己手里。

## 部署安装

我家宽带没有公网 IP，出门想记一笔工时、翻一下账单就不方便。这一篇用 [帕斯内网穿透](https://passnat.com) 把它开放到公网：先在飞牛的应用中心里装上 PassNAT 插件，在里面建一条隧道，等下部署编排的时候要用到它的地址。

### 第一步：建隧道拿地址

打开 PassNAT 插件，点创建隧道：名字随便起一个；传输协议选 HTTP；下面会多出子域名一栏，自己起一个好记的，等下要填进编排；本地地址留默认的 127.0.0.1 不用动；本地端口填 3489。点创建，这一行就有了一个自己的公网地址。

### 第二步：Docker 部署

打开 Docker 的 Compose，点新增项目，名字填 kimai，路径选一个空文件夹，来源选「创建 docker-compose.yml」，把下面的编排整段贴进去：

```yaml
services:
  sqldb:
    image: mysql:8.3
    volumes:
      - ./mysql:/var/lib/mysql
    environment:
      - MYSQL_DATABASE=kimai
      - MYSQL_USER=kimai
      - MYSQL_PASSWORD=kimai-db-2026
      - MYSQL_ROOT_PASSWORD=root-db-2026
    command: --default-storage-engine innodb
  kimai:
    image: kimai/kimai2:apache
    depends_on:
      - sqldb
    ports:
      - "3489:8001"
    volumes:
      - ./data:/opt/kimai/var/data
      - ./plugins:/opt/kimai/var/plugins
    environment:
      - APP_SECRET=change-me-probe-8f3k2m9q
      - TRUSTED_HOSTS=^换成你的子域名$
      - ADMINMAIL=nasservice@demo.local
      - ADMINPASS=kimai-admin-2026
      - DATABASE_URL=mysql://kimai:kimai-db-2026@sqldb/kimai?charset=utf8mb4&serverVersion=8.3.0
```

贴完有几处要改：TRUSTED\_HOSTS 这一行的子域名，换成你刚才隧道里起的那个；ADMINMAIL 和 ADMINPASS 是管理员账号，换成你自己的邮箱和密码；数据库的几个密码也换成自己的。

### 第三步：启动和初始化

勾上「创建项目后立即启动」，点确认。第一次启动要建数据库，等它跑一会儿。然后回到 PassNAT 插件，把这条隧道的电源点一下，绿点亮了，公网地址就通了。

用公网地址打开，账号就是编排里设的那个。第一次进来是设置向导：语言选简体中文，时区选上海，一路下一步就进主界面了。

### 记第一笔工时

先建客户：管理菜单里点客户，新建，填客户的名字保存；再点项目，新建，挂在这个客户下面——帮这个客户干的活，就是一个项目。

干活之前在首页选好客户和项目，点开始，计时器就走起来；干完点停，这一条工时就记下了。到了月底，按项目一筛，每个客户干了多久清清楚楚，点导出还能直接生成账单发给客户。

本篇教程结束

---

原文：<https://www.passnat.com/posts/kimai-open-source-time-tracking-deployment-guide-nas-simple-configuration>
